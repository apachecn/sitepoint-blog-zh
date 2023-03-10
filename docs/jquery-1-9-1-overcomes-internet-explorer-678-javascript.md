# jQuery 1.9.1 如何克服 Internet Explorer 6/7/8 JavaScript

> 原文：<https://www.sitepoint.com/jquery-1-9-1-overcomes-internet-explorer-678-javascript/>

jQuery 1.9.1 如何支持旧的 Internet Explorer？这篇文章包含了一组直接来自 jQuery 库的代码片段。希望通过通读代码，可以帮助您了解更多关于普通 JavaScript 的知识，以及 jQuery 1.9.1 如何克服 JavaScript 浏览器与 Internet Explorer 6，7，8，9 的不兼容性。

我通过一个 grep 对“IE/ie6/ie7/ie8/ie9”进行过滤，提取出 IE 相关的代码段进行分析。然后将代码片段分成一些常见的 IE 修复相关领域。通读并**了解 jQuery 为支持 Internet Explorer** 所付出的巨大努力。jQuery 团队获得了巨大的荣誉。

如果你想知道如何[迁移到 jQuery 2.0](http://www.jquery4u.com/ie/2013-jquery-internet-explorer-support/) 来支持 IE。

## jQuery 1.9.1 中的 IE6/7/8/9 修复

代码片段被分成以下几个部分。

*   IE 内存修复
*   IE 事件修复
*   IE 选择器修复
*   固定 CSS
*   不透明固定
*   IE 属性修复
*   IE 类型修复
*   IE 克隆修复
*   IE 空白修复
*   IE 表修复
*   IE 输入修复
*   IE 窗口修复
*   IE 错误修复
*   IE 协议修复
*   IE 脚本修复
*   IE 杂项修复

## IE 内存泄漏修复

```
Line 1538:

    // Null elements to avoid leaks in IE
    container = div = tds = marginDiv = null;
  });

  // Null elements to avoid leaks in IE
  all = select = fragment = opt = a = input = null;

Line 2753:

    // Add elem as a property of the handle fn to prevent a memory leak with IE non-native events
    eventHandle.elem = elem;

Line 2823:

    // Nullify elem to prevent memory leaks in IE
    elem = null;

Line 3322:

      // #8545, #7054, preventing memory leaks for custom events in IE6-8
      // detachEvent needed property on element, by name of that event, to properly expose it to GC
      if ( typeof elem[ name ] === core_strundefined ) {
        elem[ name ] = null;
      }

      elem.detachEvent( name, handle );

Line 8316:

  // Handle memory leak in IE
  script.onload = script.onreadystatechange = null;

Line 3874:

  /**
   * Support testing using an element
   * @param {Function} fn Passed the created div and expects a boolean result
   */
  function assert( fn ) {
    var div = document.createElement("div");

    try {
      return fn( div );
    } catch (e) {
      return false;
    } finally {
      // release memory in IE
      div = null;
    }
  }
```

## IE 事件修复

```
Line 97:

  // The ready event handler
  completed = function( event ) {

    // readyState === "complete" is good enough for us to call the dom ready in oldIE
    if ( document.addEventListener || event.type === "load" || document.readyState === "complete" ) {
      detach();
      jQuery.ready();
    }
  },

Line 905:

    // If IE event model is used
    } else {
      // Ensure firing before onload, maybe late but safe also for iframes
      document.attachEvent( "onreadystatechange", completed );

      // A fallback to window.onload, that will always work
      window.attachEvent( "onload", completed );

      // If IE and not a frame
      // continually check to see if the document is ready
      var top = false;

      try {
        top = window.frameElement == null && document.documentElement;
      } catch(e) {}

      if ( top && top.doScroll ) {
        (function doScrollCheck() {
          if ( !jQuery.isReady ) {

            try {
              // Use the trick by Diego Perini
              // http://javascript.nwbox.com/IEContentLoaded/
              top.doScroll("left");
            } catch(e) {
              return setTimeout( doScrollCheck, 50 );
            }

            // detach all dom ready events
            detach();

            // and execute any waiting functions
            jQuery.ready();
          }
        })();
      }
    }

Line 1430:

  // Support: IE<9
  // Opera does not clone events (and typeof div.attachEvent === undefined).
  // IE9-10 clones events bound via attachEvent, but they don't trigger with .click()
  if ( div.attachEvent ) {
    div.attachEvent( "onclick", function() {
      support.noCloneEvent = false;
    });

    div.cloneNode( true ).click();
  }

  // Support: IE<9 (lack submit/change bubble), Firefox 17+ (lack focusin event)
  // Beware of CSP restrictions (https://developer.mozilla.org/en/Security/CSP), test/csp.php
  for ( i in { submit: true, change: true, focusin: true }) {
    div.setAttribute( eventName = "on" + i, "t" );

    support[ i + "Bubbles" ] = eventName in window || div.attributes[ eventName ].expando === false;
  }

Line 3003:

    // Call a native DOM method on the target with the same name name as the event.
    // Can't use an .isFunction() check here because IE6/7 fails that test.
    // Don't do default actions on window, that's where global variables be (#6170)
    if ( ontype && elem[ type ] && !jQuery.isWindow( elem ) ) {

      // Don't re-trigger an onFOO event when we call its FOO() method
      tmp = elem[ ontype ];

      if ( tmp ) {
        elem[ ontype ] = null;
      }

      // Prevent re-triggering of the same event, since we already bubbled it above
      jQuery.event.triggered = type;
      try {
        elem[ type ]();
      } catch ( e ) {
        // IE<9 dies on focus/blur to hidden element (#1486,#12518)
        // only reproducible on winXP IE8 native, not IE9 in IE8 mode
      }
      jQuery.event.triggered = undefined;

      if ( tmp ) {
        elem[ ontype ] = tmp;
      }
    }

Line 3168:

    // Support: IE<9
    // Fix target property (#1925)
    if ( !event.target ) {
      event.target = originalEvent.srcElement || document;
    }

Line 3180:

    // Support: IE<9
    // For mouse/key events, metaKey==false if it's undefined (#3368, #11328)
    event.metaKey = !!event.metaKey;

Line 3253:

    trigger: function() {
      if ( this !== document.activeElement && this.focus ) {
        try {
          this.focus();
          return false;
        } catch ( e ) {
          // Support: IE<9
          // If we error on focus to hidden element (#1486, #12518),
          // let .trigger() run the handlers
        }
      }
    },

Line 3384:

    // Support: IE
    // Otherwise set the returnValue property of the original event to false
    } else {
      e.returnValue = false;
    }

Line 3402:

    // Support: IE
    // Set the cancelBubble property of the original event to true
    e.cancelBubble = true;

Line 3439:

  // IE submit delegation
  if ( !jQuery.support.submitBubbles ) {

    jQuery.event.special.submit = {
      setup: function() {
        // Only need this for delegated form submit events
        if ( jQuery.nodeName( this, "form" ) ) {
          return false;
        }

        // Lazy-add a submit handler when a descendant form may potentially be submitted
        jQuery.event.add( this, "click._submit keypress._submit", function( e ) {
          // Node name check avoids a VML-related crash in IE (#9807)
          var elem = e.target,
            form = jQuery.nodeName( elem, "input" ) || jQuery.nodeName( elem, "button" ) ? elem.form : undefined;
          if ( form && !jQuery._data( form, "submitBubbles" ) ) {
            jQuery.event.add( form, "submit._submit", function( event ) {
              event._submit_bubble = true;
            });
            jQuery._data( form, "submitBubbles", true );
          }
        });
        // return undefined since we don't need an event listener
      },

      postDispatch: function( event ) {
        // If form was submitted by the user, bubble the event up the tree
        if ( event._submit_bubble ) {
          delete event._submit_bubble;
          if ( this.parentNode && !event.isTrigger ) {
            jQuery.event.simulate( "submit", this.parentNode, event, true );
          }
        }
      },

      teardown: function() {
        // Only need this for delegated form submit events
        if ( jQuery.nodeName( this, "form" ) ) {
          return false;
        }

        // Remove delegated handlers; cleanData eventually reaps submit handlers attached above
        jQuery.event.remove( this, "._submit" );
      }
    };
  }

Line 3486:

// IE change delegation and checkbox/radio fix
if ( !jQuery.support.changeBubbles ) {

  jQuery.event.special.change = {

    setup: function() {

      if ( rformElems.test( this.nodeName ) ) {
        // IE doesn't fire change on a check/radio until blur; trigger it on click
        // after a propertychange. Eat the blur-change in special.change.handle.
        // This still fires onchange a second time for check/radio after blur.
        if ( this.type === "checkbox" || this.type === "radio" ) {
          jQuery.event.add( this, "propertychange._change", function( event ) {
            if ( event.originalEvent.propertyName === "checked" ) {
              this._just_changed = true;
            }
          });
          jQuery.event.add( this, "click._change", function( event ) {
            if ( this._just_changed && !event.isTrigger ) {
              this._just_changed = false;
            }
            // Allow triggered, simulated change events (#11500)
            jQuery.event.simulate( "change", this, event, true );
          });
        }
        return false;
      }
      // Delegated event; lazy-add a change handler on descendant inputs
      jQuery.event.add( this, "beforeactivate._change", function( e ) {
        var elem = e.target;

        if ( rformElems.test( elem.nodeName ) && !jQuery._data( elem, "changeBubbles" ) ) {
          jQuery.event.add( elem, "change._change", function( event ) {
            if ( this.parentNode && !event.isSimulated && !event.isTrigger ) {
              jQuery.event.simulate( "change", this.parentNode, event, true );
            }
          });
          jQuery._data( elem, "changeBubbles", true );
        }
      });
    },

    handle: function( event ) {
      var elem = event.target;

      // Swallow native change events from checkbox/radio, we already triggered them above
      if ( this !== elem || event.isSimulated || event.isTrigger || (elem.type !== "radio" && elem.type !== "checkbox") ) {
        return event.handleObj.handler.apply( this, arguments );
      }
    },

    teardown: function() {
      jQuery.event.remove( this, "._change" );

      return !rformElems.test( this.nodeName );
    }
  };
}
```

## IE 选择器修复

```
Line 177:

  if ( elem && elem.parentNode ) {
    // Handle the case where IE and Opera return items
    // by name instead of ID
    if ( elem.id !== match[2] ) {
      return rootjQuery.find( selector );
    }

    // Otherwise, we inject the element directly into the jQuery object
    this.length = 1;
    this[0] = elem;
  }

Line 3922:

    // Handle the case where IE, Opera, and Webkit return items
    // by name instead of ID
    if ( elem.id === m ) {
      results.push( elem );
      return results;
    }

Line 4195:

    // IE8 - Some boolean attributes are not treated correctly
    if ( !div.querySelectorAll("[selected]").length ) {
      rbuggyQSA.push( "\[" + whitespace + "*(?:checked|disabled|ismap|multiple|readonly|selected|value)" );
    }

Line 4202:

    // IE8 throws error here and will not see later tests
    if ( !div.querySelectorAll(":checked").length ) {
      rbuggyQSA.push(":checked");
    }

Line 4218:

    // IE8 throws error here and will not see later tests
    if ( !div.querySelectorAll(":enabled").length ) {
      rbuggyQSA.push( ":enabled", ":disabled" );
    }

Line 4236:

    // Check to see if it's possible to do matchesSelector
    // on a disconnected node (IE 9)
    support.disconnectedMatch = matches.call( div, "div" );

Line 4377:

    // IE 9's matchesSelector returns false on disconnected nodes
    if ( ret || support.disconnectedMatch ||
        // As well, disconnected nodes are said to be in a document
        // fragment in IE 9
        elem.document && elem.document.nodeType !== 11 ) {
      return ret;
    }

Line 4449:

  function siblingCheck( a, b ) {
    var cur = b && a,
      diff = cur && ( ~b.sourceIndex || MAX_NEGATIVE ) - ( ~a.sourceIndex || MAX_NEGATIVE );

    // Use IE sourceIndex if available on both nodes
    if ( diff ) {
      return diff;
    }
```

## 固定 CSS

```
Line 1360:

    // Get the style information from getAttribute
    // (IE uses .cssText instead)
    style: /top/.test( a.getAttribute("style") ),

    // Verify style float existence
    // (IE uses styleFloat instead of cssFloat)
    cssFloat: !!a.style.cssFloat,

Line 1512:

    if ( typeof div.style.zoom !== core_strundefined ) {
      // Support: IE<8
      // Check if natively block-level elements act like inline-block
      // elements when setting their display to 'inline' and giving
      // them layout
      div.innerHTML = "";
      div.style.cssText = divReset + "width:1px;padding:1px;display:inline;zoom:1";
      support.inlineBlockNeedsLayout = ( div.offsetWidth === 3 );

      // Support: IE6
      // Check if elements with layout shrink-wrap their children
      div.style.display = "block";
      div.innerHTML = "`";
      div.firstChild.style.width = "5px";
      support.shrinkWrapBlocks = ( div.offsetWidth !== 3 );

      if ( support.inlineBlockNeedsLayout ) {
        // Prevent IE 6 from affecting layout for positioned elements #11048
        // Prevent IE from shrinking the body in IE 7 mode #12869
        // Support: IE<8
        body.style.zoom = 1;
      }
    }

Line 2637:

  if ( !jQuery.support.style ) {
    jQuery.attrHooks.style = {
      get: function( elem ) {
        // Return undefined in the case of empty string
        // Note: IE uppercases css property names, but if we were to .toLowerCase()
        // .cssText, that would destroy case senstitivity in URL's, like in "background"
        return elem.style.cssText || undefined;
      },
      set: function( elem, value ) {
        return ( elem.style.cssText = value + "" );
      }
    };
  }

Line 6933:

    // getPropertyValue is only needed for .css('filter') in IE9, see #12537
    ret = computed ? computed.getPropertyValue( name ) || computed[ name ] : undefined,

Line 8917:

    // Record all 3 overflow attributes because IE does not
    // change the overflow attribute when overflowX and
    // overflowY are set to the same value
    opts.overflow = [ style.overflow, style.overflowX, style.overflowY ];

Line 9087:

  // Remove in 2.0 - this supports IE8's panic based approach
  // to setting things on disconnected nodes

  Tween.propHooks.scrollTop = Tween.propHooks.scrollLeft = {
    set: function( tween ) {
      if ( tween.elem.nodeType && tween.elem.parentNode ) {
        tween.elem[ tween.prop ] = tween.now;
      }
    }
  };

Line 9555:

  // Either scroll[Width/Height] or offset[Width/Height] or client[Width/Height], whichever is greatest
  // unfortunately, this causes bug #3838 in IE6/8 only, but there is currently no good, small way to fix it.
  return Math.max(
    elem.body[ "scroll" + name ], doc[ "scroll" + name ],
    elem.body[ "offset" + name ], doc[ "offset" + name ],
    doc[ "client" + name ]
  );`

## 不透明固定

```
Line 1360:

    // Make sure that element opacity exists
    // (IE uses filter instead)
    // Use a regex to work around a WebKit issue. See #5145
    opacity: /^0.5/.test( a.style.opacity ),

Line 7178:

    // IE uses filters for opacity
    return ropacity.test( (computed && elem.currentStyle ? elem.currentStyle.filter : elem.style.filter) || "" ) ?
      ( 0.01 * parseFloat( RegExp.$1 ) ) + "" :
      computed ? "1" : "";

Line 7190:

    // IE has trouble with opacity if it does not have layout
    // Force it by setting the zoom level
    style.zoom = 1;
```

## IE 属性/特性修复

```
Line 1332:

    // Test setAttribute on camelCase class. If it works, we need attrFixes when doing get/setAttribute (ie6/7)
    getSetAttribute: div.className !== "t",

Line 2060:

  removeProp: function( name ) {
    name = jQuery.propFix[ name ] || name;
    return this.each(function() {
      // try/catch handles cases where IE balks (such as removing a property on window)
      try {
        this[ name ] = undefined;
        delete this[ name ];
      } catch( e ) {}
    });
  },

Line 2289:

    // oldIE doesn't update selected after form reset (#2551)
    if ( ( option.selected || i === index ) &&
        // Don't return options that are disabled or in a disabled optgroup
        ( jQuery.support.optDisabled ? !option.disabled : option.getAttribute("disabled") === null ) &&
        ( !option.parentNode.disabled || !jQuery.nodeName( option.parentNode, "optgroup" ) ) ) {

Line 2367:

      // In IE9+, Flash objects don't have .getAttribute (#12945)
      // Support: IE9+
      if ( typeof elem.getAttribute !== core_strundefined ) {
        ret =  elem.getAttribute( name );
      }

Line 2392:

      // Also clear defaultChecked/defaultSelected (if appropriate) for IE<8
      if ( !getSetAttribute && ruseDefault.test( name ) ) {
        elem[ jQuery.camelCase( "default-" + name ) ] =
          elem[ propName ] = false;
      } else {
        elem[ propName ] = false;
      }

Line 2524:

    } else if ( getSetInput && getSetAttribute || !ruseDefault.test( name ) ) {
      // IE<8 needs the *property* name
      elem.setAttribute( !getSetAttribute && jQuery.propFix[ name ] || name, name );

    // Use defaultChecked and defaultSelected for oldIE
    } else {
      elem[ jQuery.camelCase( "default-" + name ) ] = elem[ name ] = true;
    }

Line 2537:

// fix oldIE value attroperty
if ( !getSetInput || !getSetAttribute ) {
  jQuery.attrHooks.value = {
    get: function( elem, name ) {
      var ret = elem.getAttributeNode( name );
      return jQuery.nodeName( elem, "input" ) ?

        // Ignore the value *property* by using defaultValue
        elem.defaultValue :

        ret && ret.specified ? ret.value : undefined;
    },
    set: function( elem, value, name ) {
      if ( jQuery.nodeName( elem, "input" ) ) {
        // Does not return so that setAttribute is also used
        elem.defaultValue = value;
      } else {
        // Use nodeHook if defined (#1954); otherwise setAttribute is fine
        return nodeHook && nodeHook.set( elem, value, name );
      }
    }
  };
}

Line 2561: 

// IE6/7 do not support getting/setting some attributes with get/setAttribute
if ( !getSetAttribute ) {

  // Use this for any attribute in IE6/7
  // This fixes almost every IE6/7 issue
  nodeHook = jQuery.valHooks.button = {
    get: function( elem, name ) {
      var ret = elem.getAttributeNode( name );
      return ret && ( name === "id" || name === "name" || name === "coords" ? ret.value !== "" : ret.specified ) ?
        ret.value :
        undefined;
    },
    set: function( elem, value, name ) {
      // Set the existing or create a new attribute node
      var ret = elem.getAttributeNode( name );
      if ( !ret ) {
        elem.setAttributeNode(
          (ret = elem.ownerDocument.createAttribute( name ))
        );
      }

      ret.value = value += "";

      // Break association with cloned elements by also using setAttribute (#9646)
      return name === "value" || value === elem.getAttribute( name ) ?
        value :
        undefined;
    }
  };

  // Set contenteditable to false on removals(#10429)
  // Setting to empty string throws an error as an invalid value
  jQuery.attrHooks.contenteditable = {
    get: nodeHook.get,
    set: function( elem, value, name ) {
      nodeHook.set( elem, value === "" ? false : value, name );
    }
  };

  // Set width and height to auto instead of 0 on empty string( Bug #8150 )
  // This is for removals
  jQuery.each([ "width", "height" ], function( i, name ) {
    jQuery.attrHooks[ name ] = jQuery.extend( jQuery.attrHooks[ name ], {
      set: function( elem, value ) {
        if ( value === "" ) {
          elem.setAttribute( name, "auto" );
          return value;
        }
      }
    });
  });
}

Line 2615:

  // Some attributes require a special call on IE
  // http://msdn.microsoft.com/en-us/library/ms536429%28VS.85%29.aspx
  if ( !jQuery.support.hrefNormalized ) {
    jQuery.each([ "href", "src", "width", "height" ], function( i, name ) {
      jQuery.attrHooks[ name ] = jQuery.extend( jQuery.attrHooks[ name ], {
        get: function( elem ) {
          var ret = elem.getAttribute( name, 2 );
          return ret == null ? undefined : ret;
        }
      });
    });

    // href/src property should get the full normalized URL (#10299/#12915)
    jQuery.each([ "href", "src" ], function( i, name ) {
      jQuery.propHooks[ name ] = {
        get: function( elem ) {
          return elem.getAttribute( name, 4 );
        }
      };
    });
  }

Line 4081:

  // IE6/7 return modified attributes
  Expr.attrHandle = assert(function( div ) {
    div.innerHTML = "";
    return div.firstChild && typeof div.firstChild.getAttribute !== strundefined &&
      div.firstChild.getAttribute("href") === "#";
  }) ?
    {} :
    {
      "href": function( elem ) {
        return elem.getAttribute( "href", 2 );
      },
      "type": function( elem ) {
        return elem.getAttribute("type");
      }
    };

Line 4950:

    "text": function( elem ) {
      var attr;
      // IE6 and 7 will map elem.type to 'text' for new HTML5 types (search, etc)
      // use getAttribute instead to test this case
      return elem.nodeName.toLowerCase() === "input" &&
        elem.type === "text" &&
        ( (attr = elem.getAttribute("type")) == null || attr.toLowerCase() === elem.type );
    },

Line 6597:

    // IE does not allow us to delete expando properties from nodes,
    // nor does it have a removeAttribute function on Document nodes;
    // we must handle all of these cases
    if ( deleteExpando ) {
      delete elem[ internalKey ];

    } else if ( typeof elem.removeAttribute !== core_strundefined ) {
      elem.removeAttribute( internalKey );

    } else {
      elem[ internalKey ] = null;
    }

Line 7200:

    // Setting style.filter to null, "" & " " still leave "filter:" in the cssText
    // if "filter:" is present at all, clearType is disabled, we want to avoid this
    // style.removeAttribute is IE Only, but so apparently is this code path...
    style.removeAttribute( "filter" );
```

## IE 类型/解析修复

```
Line 28:

  // Support: IE<9
  // For `typeof node.method` instead of `node.method !== undefined`
  core_strundefined = typeof undefined,

Line 469:

  isPlainObject: function( obj ) {
    // Must be an Object.
    // Because of IE, we also have to check the presence of the constructor property.
    // Make sure that DOM nodes and window objects don't pass through, as well
    if ( !obj || jQuery.type(obj) !== "object" || obj.nodeType || jQuery.isWindow( obj ) ) {
      return false;
    }

    try {
      // Not own constructor property must be Object
      if ( obj.constructor &&
        !core_hasOwn.call(obj, "constructor") &&
        !core_hasOwn.call(obj.constructor.prototype, "isPrototypeOf") ) {
        return false;
      }
    } catch ( e ) {
      // IE8,9 Will throw exceptions on certain host objects #9897
      return false;
    }

    // Own properties are enumerated firstly, so to speed up,
    // if last one is own, then all properties are own.

    var key;
    for ( key in obj ) {}

    return key === undefined || core_hasOwn.call( obj, key );
  },

Line 568:

  // Cross-browser xml parsing
  parseXML: function( data ) {
    var xml, tmp;
    if ( !data || typeof data !== "string" ) {
      return null;
    }
    try {
      if ( window.DOMParser ) { // Standard
        tmp = new DOMParser();
        xml = tmp.parseFromString( data , "text/xml" );
      } else { // IE
        xml = new ActiveXObject( "Microsoft.XMLDOM" );
        xml.async = "false";
        xml.loadXML( data );
      }
    } catch( e ) {
      xml = undefined;
    }
    if ( !xml || !xml.documentElement || xml.getElementsByTagName( "parsererror" ).length ) {
      jQuery.error( "Invalid XML: " + data );
    }
    return xml;
  },

Line 1560:

    // We have to handle DOM nodes and JS objects differently because IE6-7
    // can't GC object references properly across the DOM-JS boundary
    isNode = elem.nodeType,

Line 2503:

    detail = typeof prop === "boolean" ?

      getSetInput && getSetAttribute ?
        attr != null :
        // oldIE fabricates an empty string for missing boolean attributes
        // and conflates checked/selected into attroperties
        ruseDefault.test( name ) ?
          elem[ jQuery.camelCase( "default-" + name ) ] :
          !!attr :

      // fetch an attribute node for properties not recognized as boolean
      elem.getAttributeNode( name );

Line 2671:

  // IE6/7 call enctype encoding
  if ( !jQuery.support.enctype ) {
    jQuery.propFix.enctype = "encoding";
  }

Line 3959:

    // qSA works strangely on Element-rooted queries
    // We can work around this by specifying an extra ID on the root
    // and working up from there (Thanks to Andrew Dupont for the technique)
    // IE 8 doesn't work on object elements
    if ( nodeType === 1 && context.nodeName.toLowerCase() !== "object" ) {

Line 4001:

  /**
   * Detect xml
   * @param {Element|Object} elem An element or a document
   */
  isXML = Sizzle.isXML = function( elem ) {
    // documentElement is verified for cases where it doesn't yet exist
    // (such as loading iframes in IE - #4833)
    var documentElement = elem && (elem.ownerDocument || elem).documentElement;
    return documentElement ? documentElement.nodeName !== "HTML" : false;
  };

Line 4042:

    // IE8 returns a string for some attributes even when not present
    return type !== "boolean" && type !== "string";

Line 6344:

    // Modern browsers can apply jQuery collections as arrays, but oldIE needs a .get()
    core_push.apply( ret, elems.get() );
```

## IE 克隆修复

```
Line 6259:

  function fixCloneNodeIssues( src, dest ) {
    var nodeName, e, data;

    // We do not need to do anything for non-Elements
    if ( dest.nodeType !== 1 ) {
      return;
    }

    nodeName = dest.nodeName.toLowerCase();

    // IE6-8 copies events bound via attachEvent when using cloneNode.
    if ( !jQuery.support.noCloneEvent && dest[ jQuery.expando ] ) {
      data = jQuery._data( dest );

      for ( e in data.events ) {
        jQuery.removeEvent( dest, e, data.handle );
      }

      // Event data gets referenced instead of copied if the expando gets copied too
      dest.removeAttribute( jQuery.expando );
    }

    // IE blanks contents when cloning scripts, and tries to evaluate newly-set text
    if ( nodeName === "script" && dest.text !== src.text ) {
      disableScript( dest ).text = src.text;
      restoreScript( dest );

    // IE6-10 improperly clones children of object elements using classid.
    // IE10 throws NoModificationAllowedError if parent is null, #12132.
    } else if ( nodeName === "object" ) {
      if ( dest.parentNode ) {
        dest.outerHTML = src.outerHTML;
      }

      // This path appears unavoidable for IE9\. When cloning an object
      // element in IE9, the outerHTML strategy above is not sufficient.
      // If the src has innerHTML and the destination does not,
      // copy the src.innerHTML into the dest.innerHTML. #10324
      if ( jQuery.support.html5Clone && ( src.innerHTML && !jQuery.trim(dest.innerHTML) ) ) {
        dest.innerHTML = src.innerHTML;
      }

    } else if ( nodeName === "input" && manipulation_rcheckableType.test( src.type ) ) {
      // IE6-8 fails to persist the checked state of a cloned checkbox
      // or radio button. Worse, IE6-7 fail to give the cloned element
      // a checked appearance if the defaultChecked value isn't also set

      dest.defaultChecked = dest.checked = src.checked;

      // IE6-7 get confused and end up setting the value of a cloned
      // checkbox/radio button to an empty string instead of "on"
      if ( dest.value !== src.value ) {
        dest.value = src.value;
      }

    // IE6-8 fails to return the selected option to the default selected
    // state when cloning options
    } else if ( nodeName === "option" ) {
      dest.defaultSelected = dest.selected = src.defaultSelected;

    // IE6-8 fails to set the defaultValue to the correct value when
    // cloning other types of input fields
    } else if ( nodeName === "input" || nodeName === "textarea" ) {
      dest.defaultValue = src.defaultValue;
    }
  }

Line 6389:

    // IEIE Whitespace Fixes

[js]
Line 550:

      // Make sure leading/trailing whitespace is removed (IE can't handle it)
      data = jQuery.trim( data );

Line 1335:

    // IE strips leading whitespace when .innerHTML is used
    leadingWhitespace: div.firstChild.nodeType === 3,

Line 6477:

      // Manually add leading whitespace removed by IE
      if ( !jQuery.support.leadingWhitespace && rleadingWhitespace.test( elem ) ) {
        nodes.push( context.createTextNode( rleadingWhitespace.exec( elem )[0] ) );
      }

Line 7419:

  rheaders = /^(.*?):[ t]*([^rn]*)r?$/mg, // IE leaves an r character at EOL
```

## IE 表修复

```
Line 1340:

    // Make sure that tbody elements aren't automatically inserted
    // IE will insert them into empty tables
    tbody: !div.getElementsByTagName("tbody").length,

Line 1469:

    // Support: IE8
    // Check if table cells still have offsetWidth/Height when they are set
    // to display:none and there are still other visible table cells in a
    // table row; if so, offsetWidth/Height are not reliable for use when
    // determining if an element has been hidden directly using
    // display:none (it is still safe to use offsets if a parent element is
    // hidden; don safety goggles and see bug #4512 for more information).
    div.innerHTML = "

|  | t |

";
    tds = div.getElementsByTagName("td");
    tds[ 0 ].style.cssText = "padding:0;margin:0;border:0;display:none";
    isSupported = ( tds[ 0 ].offsetHeight === 0 );

    tds[ 0 ].style.display = "";
    tds[ 1 ].style.display = "none";

    // Support: IE8
    // Check if empty table cells still have offsetWidth/Height
    support.reliableHiddenOffsets = isSupported && ( tds[ 0 ].offsetHeight === 0 );

Line 6482:

    // Remove IE's autoinserted  from table fragments
    if ( !jQuery.support.tbody ) {

      // String was a , *may* have spurious 
      elem = tag === "table" && !rtbody.test( elem ) ?
        tmp.firstChild :

        // String was a bare  or 
        wrap[1] === "

" && !rtbody.test( elem ) ?
          tmp :
          0;

      j = elem && elem.childNodes.length;
      while ( j-- ) {
        if ( jQuery.nodeName( (tbody = elem.childNodes[j]), "tbody" ) && !tbody.childNodes.length ) {
          elem.removeChild( tbody );
        }
      }
    }

## IE 输入修复

```
Line 1360:

    // Check the default checkbox/radio value ("" on WebKit; "on" elsewhere)
    checkOn: !!input.value,

Line 2410:

  attrHooks: {
    type: {
      set: function( elem, value ) {
        if ( !jQuery.support.radioValue && value === "radio" && jQuery.nodeName(elem, "input") ) {
          // Setting the type on a radio button after the value resets the value in IE6-9
          // Reset value to default in case type is set after value during creation
          var val = elem.value;
          elem.setAttribute( "type", value );
          if ( val ) {
            elem.value = val;
          }
          return value;
        }
      }
    }
  },

Line 3486:

  // IE doesn't fire change on a check/radio until blur; trigger it on click
  // after a propertychange. Eat the blur-change in special.change.handle.
  // This still fires onchange a second time for check/radio after blur.
  if ( this.type === "checkbox" || this.type === "radio" ) {
    jQuery.event.add( this, "propertychange._change", function( event ) {
      if ( event.originalEvent.propertyName === "checked" ) {
        this._just_changed = true;
      }
    });

  ...

  // Check if a disconnected checkbox will retain its checked
  // value of true after appended to the DOM (IE6/7)
  support.appendChecked = input.checked;

Line 4188:

    // Select is set to empty string on purpose
    // This is to test IE's treatment of not explictly
    // setting a boolean content attribute,
    // since its presence should be enough
    // http://bugs.jquery.com/ticket/12359
    div.innerHTML = "";

Line 6523:

    handle: function( event ) {
      var elem = event.target;

      // Swallow native change events from checkbox/radio, we already triggered them above
      if ( this !== elem || event.isSimulated || event.isTrigger || (elem.type !== "radio" && elem.type !== "checkbox") ) {
        return event.handleObj.handler.apply( this, arguments );
      }
    },

Line 6523:

    // Reset defaultChecked for any radios and checkboxes
    // about to be appended to the DOM in IE 6/7 (#8060)
    if ( !jQuery.support.appendChecked ) {
      jQuery.grep( getAll( nodes, "input" ), fixDefaultChecked );
    }
```  `## IE 窗口/位置修复

```
Line 7450:

  // #8138, IE may throw an exception when accessing
  // a field from window.location if document.domain has been set
  try {
    ajaxLocation = location.href;
  } catch( e ) {
    // Use the href attribute of an A element
    // since IE will modify it given document.location
    ajaxLocation = document.createElement( "a" );
    ajaxLocation.href = "";
    ajaxLocation = ajaxLocation.href;
  }
```

## IE 错误修复` 

```
`Line 6848:

    // Wrapped to prevent IE from throwing errors when 'invalid' values are provided
    // Fixes bug #5509
    try {
      style[ name ] = value;
    } catch(e) {}

Line 7583:

    // If a selector was specified, locate the right elements in a dummy div
    // Exclude scripts to avoid IE 'Permission Denied' errors
    jQuery("```").append( jQuery.parseHTML( responseText ) ).find( selector )

```

## IE 协议/URL 修复

```
Line 7839:

    // Remove hash character (#7531: and string promotion)
    // Add protocol if not provided (#5866: IE7 issue with protocol-less urls)
    // Handle falsy url in the settings object (#10093: consistency with old signature)
    // We also use the url parameter if available
    s.url = ( ( url || s.url || ajaxLocation ) + "" ).replace( rhash, "" ).replace( rprotocol, ajaxLocParts[ 1 ] + "//" );
```

## IE 脚本/Ajax 修复

```
Line 8334:

    // Circumvent IE6 bugs with base elements (#2709 and #4378) by prepending
    // Use native DOM manipulation to avoid our domManip AJAX trickery
    head.insertBefore( script, head.firstChild );

Line 8427:

  var xhrCallbacks, xhrSupported,
    xhrId = 0,
    // #5280: Internet Explorer will keep connections alive if we don't abort on unload
    xhrOnUnloadAbort = window.ActiveXObject && function() {
      // Abort all pending requests
      var key;
      for ( key in xhrCallbacks ) {
        xhrCallbacks[ key ]( undefined, true );
      }
    };

  // Functions to create xhrs
  function createStandardXHR() {
    try {
      return new window.XMLHttpRequest();
    } catch( e ) {}
  }

  function createActiveXHR() {
    try {
      return new window.ActiveXObject("Microsoft.XMLHTTP");
    } catch( e ) {}
  }

  // Create the request object
  // (This is still attached to ajaxSettings for backward compatibility)
  jQuery.ajaxSettings.xhr = window.ActiveXObject ?
    /* Microsoft failed to properly
     * implement the XMLHttpRequest in IE7 (can't request local files),
     * so we use the ActiveXObject when it is available
     * Additionally XMLHttpRequest can be disabled in IE7/IE8 so
     * we need a fallback.
     */
    function() {
      return !this.isLocal && createStandardXHR() || createActiveXHR();
    } :
    // For all other browsers, use the standard XMLHttpRequest object
    createStandardXHR;

Line 8562:

    // When requesting binary data, IE6-9 will throw an exception
    // on any attempt to access responseText (#11426)
    if ( typeof xhr.responseText === "string" ) {
      responses.text = xhr.responseText;
    }

Line 8584:

    // IE - #1450: sometimes returns 1223 when it should be 204
    } else if ( status === 1223 ) {
      status = 204;
    }

Line 8605:

    } else if ( xhr.readyState === 4 ) {
      // (IE6 & IE7) if it's in cache and has been
      // retrieved directly we need to fire the callback
      setTimeout( callback );
```

## IE 杂项修复

```
Line 71:

  // Make sure we trim BOM and NBSP (here's looking at you, Safari 5.0 and IE)
  rtrim = /^[suFEFFxA0]+|[suFEFFxA0]+$/g,

Line 419:

    // Make sure body exists, at least, in case IE gets a little overzealous (ticket #5443).
    if ( !document.body ) {
      return setTimeout( jQuery.ready );
    }

Line 441:

  // See test/unit/core.js for details concerning isFunction.
  // Since version 1.3, DOM methods and functions like alert
  // aren't supported. They return false on IE (#2968).
  isFunction: function( obj ) {
    return jQuery.type(obj) === "function";
  },

Line 1332:

    // Make sure that link elements get serialized correctly by innerHTML
    // This requires a wrapper element in IE
    htmlSerialize: !!div.getElementsByTagName("link").length,

    // Make sure that URLs aren't manipulated
    // (IE normalizes it by default)
    hrefNormalized: a.getAttribute("href") === "/a",

    // Make sure that a selected-by-default option has a working selected property.
    // (WebKit defaults to false instead of true, IE too, if it's in an optgroup)
    optSelected: opt.selected,

Line 1399;

  // Support: IE<9
  try {
    delete div.test;
  } catch( e ) {
    support.deleteExpando = false;
  }

Line 5863:

    // IE6-8 can't serialize link, script, style, or any html5 (NoScope) tags,
    // unless wrapped in a div with non-breaking characters in front of it.
    _default: jQuery.support.htmlSerialize ? [ 0, "", "" ] : [ 1, "X", ""  ]

Line 6018:

    // If this is a select, ensure that it displays empty (#12336)
    // Support: IE<9
    if ( elem.options && jQuery.nodeName( elem, "select" ) ) {
      elem.options.length = 0;
    }

Line 6504:

    // Fix #12392 for WebKit and IE > 9
    tmp.textContent = "";
```

你可以在这里获得最新的 IE 浏览器更新:[http://blogs.msdn.com/b/ie/](http://blogs.msdn.com/b/ie/)

感谢您的阅读，请留下您的评论。`` 

## 分享这篇文章

```

```

```