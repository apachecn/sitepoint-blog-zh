# PHP 中的交互式 CLI 密码提示

> 原文：<https://www.sitepoint.com/interactive-cli-password-prompt-in-php/>

只是一个小提示，因为我最近花了很长时间才弄明白。当作为 CLI 运行时，PHP 没有提供交互式密码提示的固有方式。然而，您可以使用 bash 来完成这项任务。当然，这意味着它不能在 Windows 上运行，但是你应该可以在其他系统上运行。编辑:使用 Windows Scripting Host，可以弹出提示让用户输入。我已经扩展了这个例子，将这种技术用于基于 Windows 的系统。

```
 /**
 * Interactively prompts for input without echoing to the terminal.
 * Requires a bash shell or Windows and won't work with
 * safe_mode settings (Uses `shell_exec`)
 */
function prompt_silent($prompt = "Enter Password:") {
  if (preg_match('/^win/i', PHP_OS)) {
    $vbscript = sys_get_temp_dir() . 'prompt_password.vbs';
    file_put_contents(
      $vbscript, 'wscript.echo(InputBox("'
      . addslashes($prompt)
      . '", "", "password here"))');
    $command = "cscript //nologo " . escapeshellarg($vbscript);
    $password = rtrim(shell_exec($command));
    unlink($vbscript);
    return $password;
  } else {
    $command = "/usr/bin/env bash -c 'echo OK'";
    if (rtrim(shell_exec($command)) !== 'OK') {
      trigger_error("Can't invoke bash");
      return;
    }
    $command = "/usr/bin/env bash -c 'read -s -p ""
      . addslashes($prompt)
      . "" mypassword && echo $mypassword'";
    $password = rtrim(shell_exec($command));
    echo "n";
    return $password;
  }
} 
```

## 分享这篇文章