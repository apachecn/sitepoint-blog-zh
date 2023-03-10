# Emacs 及更高版本的 PHP 令牌索引

> 原文：<https://www.sitepoint.com/index-of-php-tokens-for-emacs-and-beyond/>

我使用 Emacs 作为我的主要编辑已经有一段时间了。很多人更喜欢 IDE，但是我从来没有对它们感到舒服过。虽然我有点喜欢在文件中显示类&函数列表的能力。Emacs 可以使用 [ctags](http://ctags.sourceforge.net/) 为文件生成一个令牌列表，但是我对它的输出并不满意。
你可能知道，PHP 有各种各样的功能，而且碰巧的是， [token_get_all](http://docs.php.net/manual/en/function.token-get-all.php) 提供了对 Zend Engine tokenizer 的访问。换句话说，当读取一个. php 文件时，PHP 本身使用的同一块代码。这为编写一个脚本提供了一个很好的基础，这个脚本可以解析大量的 ctags。

所以事不宜迟:`~/scripts/tokens.php`解析一个文件并打印其中的类和函数。它还打印任何文档块的一小段摘录，位于该项之前。这是一个很好的目录。

```
 <?php
if (!defined('T_UNSPECIFIED_STRING')) {
  define('T_UNSPECIFIED_STRING', -1);
}
function token_get_all_improved($data) {
  $tokens = array();
  $line = 1;
  $col = 0;
  $level = 0;
  $scope_level = null;
  $in_scope = false;
  foreach (token_get_all($data) as $token) {
    if (is_array($token)) {
      list ($token, $text) = $token;
    } else if (is_string($token)) {
      $text = $token;
      $token = T_UNSPECIFIED_STRING;
    }
    if ($token === T_CURLY_OPEN || $token === T_DOLLAR_OPEN_CURLY_BRACES || $text === '{') {
      ++$level;
      if (is_null($scope_level)) {
        $scope_level = $level;
      }
    } else if ($text == '}') {
      --$level;
      if ($in_scope && $level < $scope_level) {
        $in_scope = false;
      }
    }
    $tmp = $text;
    $num_new_lines = substr_count($tmp, "n");
    if (1 <= $num_new_lines) {
      $line += $num_new_lines;
      $col =  1;
      $tmp = substr($tmp, strrpos($tmp, "n") + 1);
      if ($tmp === false) {
        $tmp = '';
      }
    }
    $col += strlen($tmp);

    if ($token === T_INTERFACE || $token === T_CLASS) {
      $in_scope = true;
      $scope_level = null;
    }

    $xtoken = new StdClass();
    $xtoken->type = $token;
    $xtoken->text = $text;
    $xtoken->line = $line;
    $xtoken->col = $col;
    $xtoken->blockLevel = $level;
    $xtoken->isClassScope = $in_scope && !is_null($scope_level);
    $tokens[] = $xtoken;
  }
  return $tokens;
}

function docblock_excerpt($str) {
  if (preg_match('~*{2}[sn*]+(.*)~', trim($str, '/'), $matches)) {
    return $matches[1];
  }
}

function transform_tokens_to_list($tokens) {
  $buffer = null;
  $docblock = null;
  $results = array();
  $access = '+';
  foreach ($tokens as $token) {
    switch ($token->type) {
    case T_PUBLIC:
      $access = '+';
      break;
    case T_PRIVATE:
      $access = '-';
      break;
    case T_PROTECTED:
      $access = '#';
      break;
    case T_DOC_COMMENT:
      $docblock = $token->text;
      break;
    case T_INTERFACE:
    case T_CLASS:
    case T_FUNCTION:
      $buffer = $token;
      $buffer->access = $access;
      $access = '+';
      break;
    case T_STRING:
      if (!is_null($buffer)) {
        $buffer->isMember = $buffer->isClassScope || ($buffer->type != T_FUNCTION);
        $buffer->docblock = $docblock;
        $buffer->name = $token->text;
        $results[] = $buffer;
        $buffer = null;
        $docblock = null;
      }
      break;
    }
  }
  return $results;
}

function list_to_table($results) {
  $view = array();
  $last = null;
  foreach ($results as $token) {
    if ($last && ((!$token->isMember && $last->isMember) || (in_array($token->type, array(T_INTERFACE, T_CLASS))))) {
      $view[] = array('', '', '', '', '');
    }
    $last = $token;

    $view[] = array(
      $token->line,
      $token->isMember ? $token->access : '',
      strtolower(str_replace('T_', '', token_name($token->type))),
      $token->name,
      docblock_excerpt($token->docblock)
    );
  }
  return $view;
}

function format_table($map) {
  $out = array();
  $column_widths = array_fill(0, count($map[0]), 0);
  foreach ($map as $row) {
    foreach ($row as $num => $col) {
      $column_widths[$num] = max($column_widths[$num], strlen($col));
    }
  }
  foreach ($map as $row) {
    $line = '';
    foreach ($row as $num => $col) {
      $line .= str_pad($col, $column_widths[$num] + 2);
    }
    $out[] = trim($line);
  }
  return implode("n", $out);
}

print(
  format_table(
    list_to_table(
      transform_tokens_to_list(
        token_get_all_improved(
          file_get_contents(
            isset($argv[1]) ? $argv[1] : 'php://stdin')))))
  . "n"); 
```

您可以单独使用该脚本，但我还编写了一个附带的 emacs-mode，它将结果加载到一个缓冲区中，并使每一行都“可点击”，因此您可以跳转到文件中的相关位置。这也许可以改进——说到 lisp，我还是个新手。将其另存为`~/site-lisp/php-tokens.el`(或者您的自定义 elisp 脚本所在的位置)。

```
 (defvar php-tokens-mode-target-buffer nil)
(defun php-tokens-mode-find-occurrence ()
  (let ((pos (get-text-property (point) 'occur-target)))
    (when pos
      (unless (buffer-live-p (get-buffer php-tokens-mode-target-buffer))
        (error "Buffer for this occurrence was killed"))
      pos)))

(defun php-tokens-mode-goto-occurrence (&optional event)
  "Go to the occurrence the current line describes."
  (interactive (list last-nonmenu-event))
  (let ((pos
         (if (null event)
             ;; Actually `event-end' works correctly with a nil argument as
             ;; well, so we could dispense with this test, but let's not
             ;; rely on this undocumented behavior.
             (php-tokens-mode-find-occurrence)
           (with-current-buffer (window-buffer (posn-window (event-end event)))
             (save-excursion
               (goto-char (posn-point (event-end event)))
               (php-tokens-mode-find-occurrence)))))
        same-window-buffer-names
        same-window-regexps)
    (pop-to-buffer php-tokens-mode-target-buffer)
    (goto-line pos)))

(defun php-tokens-mode (buffer-name regexp)
  (pop-to-buffer buffer-name)
  (setq buffer-read-only nil)
  (let ((matchbeg 0)
        (origpt nil)
        (begpt nil)
        (contloop t)
        (endpt nil))
    (save-excursion
      (goto-char (point-min)) ;; begin searching in the buffer
      (while (and contloop (not (eobp)))
        (setq origpt (point))
        (if (setq endpt (re-search-forward regexp nil t))
            (add-text-properties (line-beginning-position) (line-end-position)
                                 (append
                                  `(mouse-face (highlight))
                                  `(occur-target ,(string-to-number (match-string 0)))))
          (setq contloop nil)))))
  (local-set-key '[return] 'php-tokens-mode-goto-occurrence)
  (local-set-key '[mouse-1] 'php-tokens-mode-goto-occurrence))

(defgroup php-tokens nil
  "Generates an overview of PHP tokens"
  :group 'convenience)

(defun php-tokens ()
  "Lists tokens for a PHP-buffer"
  (interactive)
  (let* ((source-buffername (buffer-name))
         (buffername "*php-tokens*")
         (contents (buffer-substring-no-properties (point-min) (point-max))))
    (when (get-buffer buffername)
      (kill-buffer buffername))
    (save-excursion
      (pop-to-buffer buffername)
      (with-temp-buffer
        (insert contents)
        (shell-command-on-region (point-min) (point-max)
                                 (format "php %s" (shell-quote-argument (expand-file-name "~/scripts/tokens.php")))
                                 buffername nil))
      (setq php-tokens-mode-target-buffer source-buffername)
      (php-tokens-mode buffername "^\([0-9]+\)[ ]+"))))

(provide 'php-tokens) 
```

要使用它，您必须将函数绑定到一个键。对您的`.emacs`文件进行以下修改:

```
 (require 'php-tokens)
(global-set-key '[f7] 'php-tokens) 
```

## 分享这篇文章