# 使用 Microsoft Translator 进行翻译和文本到语音转换

> 原文：<https://www.sitepoint.com/translation-text-speech-microsoft-translator/>

文本到语音转换是许多网站使用的一种流行技术，用于以交互方式提供内容。人工人声的产生被称为语音合成。尽管它非常受欢迎，但很少有语音合成服务，尤其是在寻找免费的服务时。Microsoft Translator 是我们可以用来获得具有有限功能的语音服务的服务之一。在本教程中，我们将了解如何使用 Microsoft Translator API 来翻译内容，然后使用这些内容制作音频文件。

你可以在 [Github](https://github.com/phpmasterdotcom/microsoft_translator) 上下载这个项目的完整源代码。

## 创建 Windows Azure Marketplace 应用程序

首先，我们必须在 Windows Azure 数据市场中创建一个应用程序来订阅 Microsoft Translator API。让我们开始应用程序创建过程。

**先决条件–微软电子邮件帐户**

### 第 1 步-登录您的 Azure 帐户

使用电子邮件帐户登录 [Azure Marketplace](https://datamarket.azure.com/) 。

### 步骤 2–注册应用程序

现在我们必须创建一个应用程序来使用翻译和语音服务。这类似于我们为脸书、LinkedIn 或 Twitter 等热门社交网站创建的应用程序。点击[注册应用](https://datamarket.azure.com/developer/applications/register)链接创建一个新的应用。您将看到一个类似如下的屏幕。

![](img/21eec64e91f51d5af47111b64ad8692e.png)

填写给定申请表的所有细节。您可以定义自己的**客户端 ID** 用于应用程序。**客户端密码**会自动为您生成。保持不变，并根据需要填充剩余的细节。

点击**创建**按钮，您将获得如下屏幕所示的已创建应用列表。

![](img/db45da1aa0f88c60dbb48f2ff72226ba.png)

记下应用程序要使用的**客户端 ID** 和**客户端密码**。

### 步骤 3–订阅翻译服务

接下来，我们必须订阅 Microsoft Translator 来使用 API。导航到 [Microft Translator](http://datamarket.azure.com/dataset/bing/microsofttranslator) 并订阅其中一个包。前 2，000，000 个字符是免费的，很明显我们必须用它来测试。

以下屏幕截图预览了订阅屏幕。订阅免费套餐。

![](img/bbcc76ecbde07e07e53311c91328c68e.png)

现在，我们已经完成了使用 Microsoft Translator API 的先决条件。让我们开始开发文本到语音服务。

## 正在初始化翻译 API 设置

让我们从初始化使用翻译器 API 的必要设置开始。用下面的代码创建一个名为`translation_api_initializer.php`的文件。

```
 <?php

        class TranslationApiInitializer {

            private $clientID;
            private $clientSecret;
            private $authUrl;
            private $grantType;
            private $scopeUrl;

            public function __construct() {
                $this->clientID = "Client ID";

                $this->clientSecret = "Client Secret";
                //OAuth Url.
                $this->authUrl = "https://datamarket.accesscontrol.windows.net/v2/OAuth2-13/";
                //Application Scope Url
                $this->scopeUrl = "http://api.microsofttranslator.com";
                //Application grant type
                $this->grantType = "client_credentials";
            }
        }    
    ?>
```

这里，我们已经配置了使用翻译服务的必要设置。您必须使用在应用程序注册过程中生成的客户端 ID 和客户端密码。其他参数包含身份验证 URL 和授权类型。我们可以为每个应用程序保持原样，除非它被微软正式更改。

## 生成应用程序令牌

下一个任务是生成令牌来访问翻译服务。令牌的生命周期有限，我们必须定期生成它们。让我们来看看令牌生成函数的实现。

```
 /*
     * Get the access token.
     *
     * @param string $grantType    Grant type.
     * @param string $scopeUrl     Application Scope URL.
     * @param string $clientID     Application client ID.
     * @param string $clientSecret Application client ID.
     * @param string $authUrl      Oauth Url.
     *
     * @return string.
     */

    function getTokens($grantType, $scopeUrl, $clientID, $clientSecret, $authUrl) {
        try {
            //Initialize the Curl Session.
            $ch = curl_init();
            //Create the request Array.
            $paramArr = array(
                'grant_type' => $grantType,
                'scope' => $scopeUrl,
                'client_id' => $clientID,
                'client_secret' => $clientSecret
            );
            //Create an Http Query.//
            $paramArr = http_build_query($paramArr);
            //Set the Curl URL.
            curl_setopt($ch, CURLOPT_URL, $authUrl);
            //Set HTTP POST Request.
            curl_setopt($ch, CURLOPT_POST, TRUE);
            //Set data to POST in HTTP "POST" Operation.
            curl_setopt($ch, CURLOPT_POSTFIELDS, $paramArr);
            //CURLOPT_RETURNTRANSFER- TRUE to return the transfer as a string of the return value of curl_exec().
            curl_setopt($ch, CURLOPT_RETURNTRANSFER, TRUE);
            //CURLOPT_SSL_VERIFYPEER- Set FALSE to stop cURL from verifying the peer's certificate.
            curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false);
            //Execute the  cURL session.
            $strResponse = curl_exec($ch);
            //Get the Error Code returned by Curl.
            $curlErrno = curl_errno($ch);
            if ($curlErrno) {
                $curlError = curl_error($ch);
                throw new Exception($curlError);
            }
            //Close the Curl Session.
            curl_close($ch);
            //Decode the returned JSON string.
            $objResponse = json_decode($strResponse);

            if ($objResponse->error) {
                throw new Exception($objResponse->error_description);
            }
            return $objResponse->access_token;
        } catch (Exception $e) {
            echo "Exception-" . $e->getMessage();
        }
    }
```

这里，我们使用一个名为`getTokens`的函数，它接受所有的设置作为参数。在函数内部，我们通过传递剩余的参数向定义的身份验证 url 发出 curl 请求。在成功执行时，我们可以使用
T1 来访问令牌。

## 实现可重用的 Curl 请求

一旦检索到访问令牌，我们就可以通过使用访问令牌授权请求来访问翻译功能。通常，我们使用 curl 向 API 发出请求，所以让我们为 curl 请求实现一个可重用的函数，如下面的代码所示。

```
 /*
     * Create and execute the HTTP CURL request.
     *
     * @param string $url HTTP Url.
     * @param string $authHeader Authorization Header string.
     * @param string $postData   Data to post.
     *
     * @return string.
     *
     */

    function curlRequest($url, $authHeader, $postData=''){
        //Initialize the Curl Session.
        $ch = curl_init();
        //Set the Curl url.
        curl_setopt($ch, CURLOPT_URL, $url);
        //Set the HTTP HEADER Fields.
        curl_setopt($ch, CURLOPT_HTTPHEADER, array($authHeader, "Content-Type: text/xml"));
        //CURLOPT_RETURNTRANSFER- TRUE to return the transfer as a string of the return value of curl_exec().
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, TRUE);
        //CURLOPT_SSL_VERIFYPEER- Set FALSE to stop cURL from verifying the peer's certificate.
        curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, False);
        if ($postData) {
            //Set HTTP POST Request.
            curl_setopt($ch, CURLOPT_POST, TRUE);
            //Set data to POST in HTTP "POST" Operation.
            curl_setopt($ch, CURLOPT_POSTFIELDS, $postData);
        }
        //Execute the  cURL session.
        $curlResponse = curl_exec($ch);
        //Get the Error Code returned by Curl.
        $curlErrno = curl_errno($ch);
        if ($curlErrno) {
            $curlError = curl_error($ch);
            throw new Exception($curlError);
        }
        //Close a cURL session.
        curl_close($ch);
        return $curlResponse;
    }
```

这个函数将请求 URL、请求头和 post 数据作为参数，并返回 curl 响应或错误。现在，访问翻译服务的所有必要功能都准备好了。

## 翻译内容

Microsoft Translator API 为翻译相关的功能提供了广泛的方法。在本教程中，我们将使用**翻译**和**说话**的方法。你可以在 http://msdn.microsoft.com/en-us/library/ff512419.aspx 的T5 查看完整的 API 方法。
让我们从翻译功能的实现开始。

```
 /*
     * Get the translated text
     * 
     * @param   string $text Text content for translation
     * @param   string $from_lang   Language of the text
     * @param   string $to_lang  Translation language
     * @return  array  Result set
     */

    public function textTranslate($text, $from_lang, $to_lang) {

        try {
            //Get the Access token.
            $accessToken = $this->getTokens($this->grantType, $this->scopeUrl, $this->clientID, $this->clientSecret, $this->authUrl);

            //Create the authorization Header string.
            $authHeader = "Authorization: Bearer " . $accessToken;

            //Input String.
            $inputStr = urlencode($text);

            $from = $from_lang;
            $to = $to_lang;

            //HTTP Detect Method URL.
            $detectMethodUrl = "http://api.microsofttranslator.com/V2/Http.svc/Translate?text=" . urlencode($inputStr) . 
            "&from=" . $from . "&to=" . $to."&contentType=text/plain";

            //Call the curlRequest.
            $strResponse = $this->curlRequest($detectMethodUrl, $authHeader);

            //Interprets a string of XML into an object.
            $xmlObj = simplexml_load_string($strResponse);
            foreach ((array) $xmlObj[0] as $val) {
                $translated_str = $val;
            }

            return array("status" => "success", "msg" => $translated_str);

        } catch (Exception $e) {
            return array("status" => "error", "msg" => $e->getMessage());
        }
    }
```

翻译需要翻译源语言、目的语言和文本。所以我们将它们用作`textTranslate`函数的参数。然后，我们使用之前创建的`getToken`函数来检索令牌，并使用`Authorization: Bearer`将其分配给请求头。

然后我们使用`$from_lang`和`$to_lang`变量设置源语言和目的语言。我们还必须使用 PHP 的 [urlencode](http://us2.php.net/urlencode) 函数对文本内容进行编码。

现在是时候开始翻译过程了。Translator API 提供了一个名为 Translate 的方法，我们可以使用位于[【http://api.microsofttranslator.com/V2/Http.svc/Translate】](http://api.microsofttranslator.com/V2/Http.svc/Translate)的 URL 来访问它

该方法将 appId、text、to 语言和内容类型作为强制参数。因为我们使用授权头，所以没有必要指定 **appId** 。因此，我们使用`$detectMethodUrl`变量将所有必要的参数分配给翻译 API URL。

最后，我们通过传递翻译 API URL 和授权头来初始化 curl 请求。成功执行后，我们将获得 XML 格式的翻译数据。所以我们使用`simplexml_load_string`函数加载 XML 字符串并过滤翻译后的文本。
现在我们可以在任何支持的语言之间翻译文本。

## 生成语音文件

本教程的最后一个任务是使用翻译后的文本在 mp3 文件中生成语音。我们将使用类似的技术来做到这一点。用下面的代码创建一个名为`textToSpeech`的函数。

```
 /**
     * Returns a stream of a wave-file speaking the passed-in text in the desired language.
     * @param string $text text of language to break
     * @param string $to_lang language of the text
     * @return -
     */
    public function textToSpeech($text, $to_lang) {

        try {

            //Get the Access token.
            $accessToken = $this->getTokens($this->grantType, $this->scopeUrl, $this->clientID, $this->clientSecret, $this->authUrl);

            //Create the authorization Header string.
            $authHeader = "Authorization: Bearer " . $accessToken;

            //Set the params.
            $inputStr = urlencode($text);
            $language = $to_lang;

            $params = "text=$inputStr&language=$language&format=audio/mp3";

            //HTTP Speak method URL.
            $url = "http://api.microsofttranslator.com/V2/Http.svc/Speak?$params";
            //Set the Header Content Type.
            header('Content-Type: audio/mp3');
            header('Content-Disposition: attachment; filename=' . uniqid('SPC_') . '.mp3');

            //Call the curlRequest.
            $strResponse = $this->curlRequest($url, $authHeader);
            echo $strResponse;
        } catch (Exception $e) {
            echo "Exception: " . $e->getMessage() . PHP_EOL;
        }
    }
```

这个函数本质上类似于翻译函数。这里我们使用位于 http://api.microsofttranslator.com/V2/Http.svc/Speak
的 Speak 方法 URL 来代替 translate URL。我们必须使用文本、语言和格式作为必要的参数。因为我们使用翻译的文本来生成语音，所以语言参数应该等于在`textTranslate`函数中使用的`$to_lang`。

然后我们必须使用必要的头来自动下载语音文件。这里，我们使用`audio/mp3`作为内容类型，使用`uniqid`函数生成一个惟一的文件名。现在我们已经可以使用翻译和语音功能了。

## 实现前端接口

到目前为止，我们在应用程序的后端实现了翻译和语音功能。现在我们必须构建一个简化的前端接口来访问语音文件生成服务。让我们用基本的 HTML 表单创建一个名为`index.php`的文件。

```
 <?php
        $bing_language_codes = array('da' => 'Danish',
        'de' =>'German','en'=> 'English','fi'=> 'Finnish',
        'fr'=>'French','nl'=>'Dutch','ja'=> 'Japanese','pl'=> 'Polish',                    'es'=> 'Spanish','ru'=> 'Russian',);

    ?>
    <form action='' method='POST' >
    <table>
        <tr><td>Select From Language</td>
        <td><select name='from_lang' >
        <?php foreach($bing_language_codes as $code=>$lang){ ?>
                                                    <option value='<?php echo $code; ?>'><?php echo $lang; ?></option>
        <?php } ?>
            </select>
            </td>
        </tr>
        <tr><td>Select To Language</td>
        <td><select name='to_lang' >
        <?php foreach($bing_language_codes as $code=>$lang){ ?>
            <option value='<?php echo $code; ?>'><?php echo $lang; ?></option>
        <?php } ?>
        </select>
        </td>
        </tr>
        <tr><td>Text</td>
        <td><textarea cols='50' name='text' ></textarea>
        </td>
        </tr>
        <tr><td></td>
        <td><input type='submit' value='Submit' />
        </td>
        </tr>
    </table>
    </form>
```

该表单允许用户选择首选的源语言和目标语言，并键入要转换为语音的内容。我在一个名为`$bing_language_codes`的数组中使用了一些受支持的语言。现在，我们可以转到表单提交处理流程来生成语音文件，如下所示。

```
 <?php

    include_once "translation_api_initializer.php";

    $bing_language_codes = array('da' => 'Danish',
    'de' => 'German','en'=> 'English','fi'=> 'Finnish',
    'fr'=> 'French', 'nl'=> 'Dutch','ja'=>'Japanese',
    'pl'=> 'Polish','es'=> 'Spanish','ru'=> 'Russian');

    if($_POST){

        $bing = new TranslationApiInitializer();

    $from_lang = isset($_POST['from_lang']) ? $_POST['from_lang'] : 'en';
    $to_lang = isset($_POST['to_lang']) ? $_POST['to_lang'] : 'fr';
    $text = isset($_POST['text']) ? $_POST['text'] : '';

    $result = $bing->textTranslate($text, $from_lang, $to_lang);
                                                          $bing->textToSpeech($result['msg'],$to_lang);

    }

    ?>
```

我们在流程中包含了前面创建的`TranslationApiInitializer`文件，并分别执行翻译和语音功能来生成音频文件。代码完成后，您将能够使用此服务生成音频文件。

你可以在[http://www.innovativephp.com/demo/spc_text_to_speech](http://www.innovativephp.com/demo/spc_text_to_speech)看一下现场演示

## 包裹

在本教程中，我们使用 Microsoft Translator API 实现了翻译和语音生成。尽管它很容易使用，而且在某种程度上是免费的，但也有局限性。语音服务只提供给有限数量的字符，大约在 2000 个左右。所以不可能对更大的文本块使用这个服务。我推荐你使用 [Acapela](http://www.acapela-group.com) 进行大规模的语音生成服务。

希望你喜欢这个教程，期待你的评论和建议。

## 分享这篇文章