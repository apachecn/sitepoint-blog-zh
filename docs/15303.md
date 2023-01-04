# 到 Unix 服务器的域迁移

> 原文：<https://www.sitepoint.com/domain-migration-unix-server/>

要更换网络提供商或安装新的服务器，需要迁移您域名的所有网页和其他文件。虽然我们不会在这里介绍虚拟域的实现和配置，但我们将重点关注如何将文件从您以前的服务器传输到新的服务器。

##### 为什么迁移不像看起来那么简单

乍一看，这个任务似乎很简单:只需通过 FTP 将文件下载到您的本地系统，然后将它们再次上传到新的服务器。

然而，这种方法有几个严重的缺点:

*   将不会保留文件权限
*   文件所有权将会改变
*   带有未知扩展名的 ASCII 文件可能被视为二进制文件
*   文件将以未压缩的形式下载和上传，这会浪费时间、带宽和系统资源。

如果你以这种方式进行，你将会面临很多调整工作，以使一切都像以前一样运行。这尤其适用于 CGI 脚本，其中目录和文件权限对于确保功能性至关重要。

##### 迁移变得简单

这里有一种方法可以简化您的迁移，并减少迁移过程中不必要的工作。

1.  Log in to your old server by Telnet (e.g. as user "domainowner").
2.  Navigate to your domain directory (e.g. `:cd /usr/www/htdocs/yourdomain`).
3.  Next, compress the files into an archive by entering the following on the command line:
    `tar -cpz --same-owner -f yourdomain.tar.gz * .htaccess`

    “tar”命令需要以下语法:
    `tar -function -option file
     function:   c create archive
     option:     p extract all protection information
       z compress data with gzip
       --same-owner create extracted files with the same ownership
       f use archive file`
    在我们上面的例子中，将生成归档文件“`yourdomain.tar.gz`”。你可以选择任何你喜欢的文件名，只要你坚持扩展名“`.tar.gz`”。通配符“`*`”(上面命令中的倒数第二个)指定所有文件，包括子目录中的文件，都将包含在归档文件中。但是，因为这不包括以句点开头的文件，所以您必须明确列出这些文件。在我们的例子中，这是文件“`.htaccess`”。

4.  Now, on the new server, you will have to configure user account "`domainowner`" and the directory "`/usr/www/htdocs/yourdomain`". Note that if the directory names on your previous server are not identical to those on your new server, you may have to modify the path variables in your CGI scripts. Hence, it’s strongly recommended that you simply clone directory names.
5.  Now, to upload the archive file "`yourdomain.tar.gz`" to your new system. There are two possible ways to do this:
    *   directly, by logging in to your new server and downloading the file from there using whatever FTP client is available, e.g. WU-FTP. Consult the program’s documentation to learn how to do this as we cannot cover it here.
        **or**
    *   首先将文件下载到您的本地系统，然后从那里上传到新的服务器。
6.  On the new system, log in via Telnet e.g. as user "domainowner".
7.  切换到您的域目录，例如键入:`cd /usr/www/htdocs/yourdomain`
8.  Next, you’ll need to unpack the archive file. Enter the command:

    ```
    tar -xz -f yourdomain.tar.gz 

        function:   x extract files from an archive 

        option:     z uncompress data with gzip 

                    f use archive file 

    ```

现在，您已经成功地在新系统上复制了以前熟悉的环境！

然而，我建议你在正式启动之前，彻底测试一下新域的设置，尤其是你的 CGI 脚本。

##### 同步您的系统

如果在实际启动新设置之前，先前服务器上的某些文件已被修改或添加，您可以通过以下方式同步两个系统:

1.  Log in via Telnet on your old server e.g. as user "domainowner".
2.  Switch to your domain directory, e.g. by entering:
    `cd /usr/www/htdocs/yourdomain`
3.  List all new and modified files, using this command:
    `/usr/bin/find -newer yourdomain.tar.gz > newerfiles.txt`

    该命令将这些文件与您在上面的步骤 3 中生成的归档文件进行比较。所有新的和修改过的文件现在都将列在文件“`newerfiles.txt`”中。

4.  Edit the file "`newerfiles.txt`" and delete the line "`.`" and the lines including directories, e.g.: "`./graphics`" You can do this in two ways. To complete the changes locally, download the file first and open your text editor (ASCII format only!). Or, to carry out the changes online, open your favorite online editor.

    接下来，必须使用以下命令将新的和修改过的文件打包到归档文件中:

    ```
    tar -cpz --same-owner -f yourdomain-new-files.tar.gz -T  

    newerfiles.txt 

    option:   T get names to extract or create from file F
    ```

5.  Now, upload the newly generated archive file, "`yourdomain-new-files.tar.gz`", to your new server as explained above in Step 5 above.
6.  To unpack the archive, enter this command:

    ```
    tar -xz -f yourdomain-new-files.tar.gz

    ```

恭喜你！新旧系统上的所有文件都已同步，您可以在新的托管服务器上激活迁移的域。

## 分享这篇文章