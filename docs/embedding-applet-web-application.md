# 在 Web 应用程序中嵌入 Applet

> 原文：<https://www.sitepoint.com/embedding-applet-web-application/>

applet 是一个小型的以任务为中心的应用程序，它运行在一个较大的应用程序或程序中。在 web 上，小程序通常用 Java 编写，并插入网页中以提供一些特定的功能。

本教程指导您开发一个 Java Applet，并将该 Applet 嵌入到 Web 应用程序中，使其在网站上可见。我将使用以下技术:

*   NetBeansIDE 7.3
*   Java 1.7
*   JSP (Java 服务器页面)
*   Apache Tomcat 服务器

## 介绍

在开始这篇内容丰富的教程之前，我想先介绍一下将要涉及的技术:

*   **NetBeansIDE:** 是 Oracle 公司用 Java 开发的集成开发环境。它可以运行在 Windows、Linux、Solaris 等支持兼容 Java 虚拟机(JVM)的平台上。
*   Java: 它是詹姆斯·高斯林在 1995 年开发的一种编程语言，其语法来源于 C 和 C++。
*   **Applet:** 基本上就是一个写出来在网页上运行的程序。可以创建按钮、标签、复选框、文本框等控件，也可以捕获鼠标输入。
*   **Web 应用:**Web 应用使用的客户端是 Web 浏览器。它主要使用服务器端脚本(JSP，ASP)和客户端脚本(HTML，JavaScript)的组合
*   **JSP (Java 服务器页面):** JSP 用于开发动态生成的网页。小程序将被嵌入到 JSP 页面中。
*   **Apache Tomcat Server:** 它是一个开源的 Web 服务器，实现了 JSP 和 Java Servlet 规范。

## 在 Java 应用程序中开发和运行 Applet

按照以下简单步骤，使用 NetBeansIDE 在 Java 应用程序中创建一个 Applet:

从**文件->new project->choose project->Java 应用**打开 NetBeansIDE 项目，如下图所示:

![appletfig1](img/73d88482617d2880dc03567b41b02cdb.png)
图 1:新的 Java 应用

现在，添加项目名称、项目位置:

**项目名称:**AmitJavaApplication
项目位置:C:\ Users \ Amit \ Documents \ netbeans projects
主类:AmitJavaApplication。AmitJavaApplication

![appletfig2](img/2c4b666c00a41de14a731a37b00ea52b.png)
图 2:项目名称为 AmitJavaApplication

当您单击此处的“Finish”时，会生成以下代码:

**包:**amitjavaapplication
主类: amitjavaapplication。AmitJavaApplication

```
/*
 * To change this template, choose Tools | Templates
 * and open the template in the editor.
 */
package amitjavaapplication;

/**
 *
 * @author amit
 */
public class AmitJavaApplication {

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        // TODO code application logic here
    }
}
```

可以在左侧面板的选项卡“**项目**和“**文件**下看到目录结构:

![appletfig3](img/70f7016904d1bee7b5eb64df7e58fd7d.png)
图 3:AmitJavaApplication.java 代码

要创建 Applet，请执行以下步骤:

右键点击**AmitJavaApplication->New->Other**

![appletfig4](img/fc8cf98e73ab9e12e7efa8ba41872b54.png)
图 4:创建新 Applet 的步骤

选择**Swing GUI form->JAppletForm**，点击**下一步**，如下图所示:

![appletfig5](img/e9fb05daa24b35184ba2969ecce0c473.png)
图 5:从 Swing GUI 表单中选择“JApplet 表单”

完成上述步骤后，输入以下内容:

**类名:**Amit applet
包: tutorial.me

该文件在以下位置的“tutorial.me”包下创建:

**文件位置:**C:\ Users \ Amit \ Documents \ netbeans projects \ AmitJavaApplication \ src \ tutorial \ me

点击**完成**后:

![appletfig6](img/309c759f578fc5d0b0ff11235f5ed503.png)
图 6:设置类名和包

现在这里可以看到“**AmitApplet.java**的设计画面。它带有一个选项“**调色板**”，可以将任何 Swing 容器/控件添加到 Applet 中:

![appletfig7](img/9ea9196c7101704de8b8411451a1e5b2.png)
图 Applet 类的设计部分

小程序的设计如下所示，包含以下 Swing 容器和控件:

| 摇摆容器/控制 | 数字 | 变量名 |
| 集装箱 | one | jPanel1 |
| JLabel(控制) | one | jLabel1 |
| JTextField(控制) | three | 文本编号 1
文本编号 2
文本结果 |
| JButton(控件) | one | BTN 多重 |

![appletfig8](img/fd7537756f54f7743701c91c4b7bbf21.png)
图 8:设计用于将两个数相乘的小程序

当“**设计**完成后，在“**源代码编辑器**下可以看到下面的代码，小程序应用的截图和代码如下所示:

![appletfig9](img/a8cc9a1a2fed67914e771817b3aff9c2.png)
图 9:代码截图

这里提供了完整的代码:

```
/*
 * To change this template, choose Tools | Templates
 * and open the template in the editor.
 */
package tutorial.me;

/**
 *
 * @author amit
 */
public class AmitApplet extends javax.swing.JApplet {

    /**
     * Initializes the applet AmitApplet
     */
    @Override
    public void init() {
        /* Set the Nimbus look and feel */
        //<editor-fold defaultstate="collapsed" desc=" Look and feel setting code (optional) ">
        /* If Nimbus (introduced in Java SE 6) is not available, stay with the default look and feel.
         * For details see http://download.oracle.com/javase/tutorial/uiswing/lookandfeel/plaf.html 
         */
        try {
            for (javax.swing.UIManager.LookAndFeelInfo info : javax.swing.UIManager.getInstalledLookAndFeels()) {
                if ("Nimbus".equals(info.getName())) {
                    javax.swing.UIManager.setLookAndFeel(info.getClassName());
                    break;
                }
            }
        } catch (ClassNotFoundException ex) {
            java.util.logging.Logger.getLogger(AmitApplet.class.getName()).log(java.util.logging.Level.SEVERE, null, ex);
        } catch (InstantiationException ex) {
            java.util.logging.Logger.getLogger(AmitApplet.class.getName()).log(java.util.logging.Level.SEVERE, null, ex);
        } catch (IllegalAccessException ex) {
            java.util.logging.Logger.getLogger(AmitApplet.class.getName()).log(java.util.logging.Level.SEVERE, null, ex);
        } catch (javax.swing.UnsupportedLookAndFeelException ex) {
            java.util.logging.Logger.getLogger(AmitApplet.class.getName()).log(java.util.logging.Level.SEVERE, null, ex);
        }
        //</editor-fold>

        /* Create and display the applet */
        try {
            java.awt.EventQueue.invokeAndWait(new Runnable() {
                public void run() {
                    initComponents();
                }
            });
        } catch (Exception ex) {
            ex.printStackTrace();
        }
    }

    /**
     * This method is called from within the init() method to initialize the
     * form. WARNING: Do NOT modify this code. The content of this method is
     * always regenerated by the Form Editor.
     */
    @SuppressWarnings("unchecked")
    // <editor-fold defaultstate="collapsed" desc="Generated Code">                          
    private void initComponents() {

        jPanel1 = new javax.swing.JPanel();
        jLabel1 = new javax.swing.JLabel();
        textNum1 = new javax.swing.JTextField();
        textNum2 = new javax.swing.JTextField();
        btnMultiply = new javax.swing.JButton();
        textResult = new javax.swing.JTextField();

        jLabel1.setFont(new java.awt.Font("Tahoma", 1, 14)); // NOI18N
        jLabel1.setText("Multiply 2 numbers");

        textNum1.setText("0");

        textNum2.setText("0");

        btnMultiply.setText("Multiply");
        btnMultiply.addActionListener(new java.awt.event.ActionListener() {
            public void actionPerformed(java.awt.event.ActionEvent evt) {
                btnMultiplyActionPerformed(evt);
            }
        });

        textResult.setText("Result");

        javax.swing.GroupLayout jPanel1Layout = new javax.swing.GroupLayout(jPanel1);
        jPanel1.setLayout(jPanel1Layout);
        jPanel1Layout.setHorizontalGroup(
            jPanel1Layout.createParallelGroup(javax.swing.GroupLayout.Alignment.LEADING)
            .addGroup(javax.swing.GroupLayout.Alignment.TRAILING, jPanel1Layout.createSequentialGroup()
                .addGap(0, 36, Short.MAX_VALUE)
                .addGroup(jPanel1Layout.createParallelGroup(javax.swing.GroupLayout.Alignment.LEADING)
                    .addComponent(jLabel1, javax.swing.GroupLayout.PREFERRED_SIZE, 138, javax.swing.GroupLayout.PREFERRED_SIZE)
                    .addComponent(textNum2, javax.swing.GroupLayout.PREFERRED_SIZE, 119, javax.swing.GroupLayout.PREFERRED_SIZE)
                    .addComponent(textNum1, javax.swing.GroupLayout.PREFERRED_SIZE, 119, javax.swing.GroupLayout.PREFERRED_SIZE)
                    .addGroup(jPanel1Layout.createSequentialGroup()
                        .addComponent(btnMultiply)
                        .addGap(18, 18, 18)
                        .addComponent(textResult, javax.swing.GroupLayout.PREFERRED_SIZE, 51, javax.swing.GroupLayout.PREFERRED_SIZE)))
                .addGap(19, 19, 19))
        );
        jPanel1Layout.setVerticalGroup(
            jPanel1Layout.createParallelGroup(javax.swing.GroupLayout.Alignment.LEADING)
            .addGroup(jPanel1Layout.createSequentialGroup()
                .addContainerGap()
                .addComponent(jLabel1)
                .addGap(28, 28, 28)
                .addComponent(textNum1, javax.swing.GroupLayout.PREFERRED_SIZE, javax.swing.GroupLayout.DEFAULT_SIZE,                     javax.swing.GroupLayout.PREFERRED_SIZE)
                .addPreferredGap(javax.swing.LayoutStyle.ComponentPlacement.UNRELATED)
                .addComponent(textNum2, javax.swing.GroupLayout.PREFERRED_SIZE, javax.swing.GroupLayout.DEFAULT_SIZE, javax.swing.GroupLayout.PREFERRED_SIZE)
                .addPreferredGap(javax.swing.LayoutStyle.ComponentPlacement.RELATED, 34, Short.MAX_VALUE)
                .addGroup(jPanel1Layout.createParallelGroup(javax.swing.GroupLayout.Alignment.BASELINE)
                    .addComponent(btnMultiply)
                    .addComponent(textResult, javax.swing.GroupLayout.PREFERRED_SIZE, javax.swing.GroupLayout.DEFAULT_SIZE, javax.swing.GroupLayout.PREFERRED_SIZE))
                .addGap(23, 23, 23))
        );

        javax.swing.GroupLayout layout = new javax.swing.GroupLayout(getContentPane());
        getContentPane().setLayout(layout);
        layout.setHorizontalGroup(
            layout.createParallelGroup(javax.swing.GroupLayout.Alignment.LEADING)
            .addGroup(layout.createSequentialGroup()
                .addGap(100, 100, 100)
                .addComponent(jPanel1, javax.swing.GroupLayout.PREFERRED_SIZE, javax.swing.GroupLayout.DEFAULT_SIZE, javax.swing.GroupLayout.PREFERRED_SIZE)
                .addContainerGap(155, Short.MAX_VALUE))
        );
        layout.setVerticalGroup(
            layout.createParallelGroup(javax.swing.GroupLayout.Alignment.LEADING)
            .addGroup(layout.createSequentialGroup()
                .addGap(41, 41, 41)
                .addComponent(jPanel1, javax.swing.GroupLayout.PREFERRED_SIZE, javax.swing.GroupLayout.DEFAULT_SIZE, javax.swing.GroupLayout.PREFERRED_SIZE)
                .addContainerGap(95, Short.MAX_VALUE))
        );
    }// </editor-fold>                        

    private void btnMultiplyActionPerformed(java.awt.event.ActionEvent evt) {                                            
        // TODO add your handling code here:
         int n1 = Integer.parseInt(textNum1.getText());
         int n2 = Integer.parseInt(textNum2.getText());
         int mul = n1 * n2;
         textResult.setText(Integer.toString(mul));
    }                                           

    // Variables declaration - do not modify                     
    private javax.swing.JButton btnMultiply;
    private javax.swing.JLabel jLabel1;
    private javax.swing.JPanel jPanel1;
    private javax.swing.JTextField textNum1;
    private javax.swing.JTextField textNum2;
    private javax.swing.JTextField textResult;
    // End of variables declaration                   
}
```

现在，通过右键单击 AmitApplet.java->运行文件来运行 Applet，如下所示:

![appletfig10](img/5dc56bcf559636fe7e7b76dac543a647.png)
图 10:运行小程序“AmitApplet.java”

完成上述步骤后，可以在此处看到小程序，现在输入以下值进行相乘，即

```
9 * 7 = 63
```

![appletfig11](img/45ccd44a7215e69166a090a5af6469f4.png)
图 11:小程序查看器

要在 NetBeansIDE 中运行 Applet，应该遵循上述步骤。

## 开发和运行 Web 应用程序:

按照以下简单步骤使用 NetBeansIDE 创建 Web 应用程序:

进入**文件- >新建项目**如下图所示:

![appletfig12](img/2e9358a5357ccc7fa8526af5f4092b68.png)
图 12:新建 WebApplication 项目

启动**新项目**后，从**类别**中选择 **Java Web** 。现在选择 **Web 应用**并点击**下一步**:

![appletfig13](img/455189705d8c0705f9edb8d6705cc2ee.png)
图 13:从“Java Web”类别中选择 Web 应用

完成上述步骤后，添加项目名称、项目位置:

**项目名称:**AmitWebApplication
项目位置:C:\ Users \ Amit \ Documents \ netbeans projects

现在，点击下一个的**:**

![appletfig14](img/05771f3950d1492efe8aa3cce8af58af.png)
图 14:项目名称为“AmitWebApplication”

从“服务器和设置”中选择服务器 **Apache Tomcat** 运行 **AmitWebApplication** 。

![appletfig15](img/f2e485d6e9877d23aff882705703dbbe.png)
图 15:选择服务器作为 Apache Tomcat

点击**下一个**开始**web 应用**项目。它将生成“【index.jsp】T4”文件，代码如下:

```
<%-- 
    Document   : index
    Created on : Nov 6, 2013, 10:46:24 AM
    Author     : amit
--%>

<%@page contentType="text/html" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; 
        charset=UTF-8">
        <title>JSP Page</title>
    </head>
    <body>
        <h1>Hello World!</h1>
     </body>
              </html>
```

![appletfig16](img/3ec0e9cc08cdd792fb3416b6dab7cc7e.png)
图十六:index.jsp

现在通过右键单击 **AmitWebApplication - >清理并构建**来运行 Web 应用程序，如下所示:

![appletfig17](img/ecf9195ec9ab57ca11d5289b5eb72657.png)
图 17:构建 AmitWebApplication 项目

现在，如上所示建立项目后，通过使用功能键“F6”或使用如下所示的截图运行项目:

![appletfig18](img/0d397c408d1acc1c5688998032ce1ce6.png)
图 18:运行项目

该项目将在本地主机上成功运行，即在谷歌浏览器(你可以考虑任何其他浏览器)的下列地址:

```
localhost:8080/AmitWebApplication/index.jsp
```

下面的屏幕截图显示了我们的 Web 应用程序的成功运行:

![appletfig19](img/ee01563b3064d6176672350b14f70f1f.png)
图 19:index.jsp 在本地主机上运行

在 NetBeansIDE 中运行 WebApplication 需要遵循以上步骤。

## 在 Web 应用程序中嵌入/集成 Applet

按照以下简单步骤，使用 NetBeansIDE 将 Applet 集成到 WebApplication 中:

**右键单击 AmitWebApplication- >属性**

![appletfig20](img/d525f8a1bd78008a6677a9e29e35a1f0.png)
图 20:项目属性

将会打开**“项目属性**”对话框。

从**类别**中选择**包装**，然后点击**添加项目**。

![appletfig21](img/9070956743b5de890c50934fb21bc9da.png)
图 21:转到类别下的打包，点击“添加项目”

将显示在 NetBeansIDE 中开发的项目列表。只需选择您想要添加的项目，这里我们将添加“ **AmitJavaApplication** 项目。

在添加“ **AmitJavaApplication** ”时，它会自己添加选定的项目 jar 文件，即“AmitJavaApplication.jar”，如下面给出的截图所示。之后点击**添加项目 JAR 文件**按钮:

*注意:“AmitJavaApplication”jar 文件只有在项目“AmitJavaApplication”构建后才可见。*

![appletfig22](img/652fe422a27e8804ed31ac3102952a6d.png)
图 22:导航到 AmitJavaApplication 项目

添加 jar 文件后，单击 OK，等待几秒钟，直到 jar 文件被添加到项目中。该过程如下所示。然后按下**确定**:

![appletfig23](img/042e2b47d29cf841854fedb5053d4acd.png)
图 23:添加到项目中的 jar 文件

![appletfig24](img/c5a67d9dfe7729b0be88cedd0e4f91c8.png)
图 24:web application 中 Applet 的集成

“jar”文件被添加到项目中，可以在“ **AmitWebApplication** ”项目的目录结构下看到。build/web 文件夹显示了添加的 jar 文件:

![appletfig25](img/94b07e90dd2ddf78f10a6a028a76745f.png)
图 25:显示在 AmitWebApplication 项目中添加 jar 文件的目录结构

在这里，Applet 是使用`applet`标签嵌入的:

```
<applet code="tutorial.me.AmitApplet" archive="AmitJavaApplication.jar" width="400" height="350">
</applet>
```

这里，

| 子视图属性 | 描述 | 项目中的用法 |
| 密码 | 指定的文件名 | tutorial.me.AmitApplet |
| 档案馆 | 指定存档文件(此处为 jar 文件)的位置 | AmitJavaApplication.jar |
| 高度 | 指定的子视图高度 | Three hundred and fifty |
| 宽度 | 指定的子视图宽度 | four hundred |

![appletfig26](img/f90efccc386261ade4a351d59d71e2fd.png)
图 26:添加了在 index.jsp 运行 applet 的代码

代码:

```
<%-- 
    Document   : index
    Created on : Nov 6, 2013, 10:46:24 AM
    Author     : amit
--%>

<%@page contentType="text/html" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
        <title>My first WebApplication using NetBeansIDE</title>
    </head>
    <body>
        <h1>Hello World!</h1>
        <br />
        <p>WebApplication using NetBeansIDE...</p>

        <!-- Embedding Applet -->
        <applet code="tutorial.me.AmitApplet" archive="AmitJavaApplication.jar"   
        width="400" height="350">
        </applet>
    </body>
             </html>
```

现在通过用**右键单击- > AmitWebApplication - >清理并构建**来运行 Web 应用项目，如下所示:

![appletfig27](img/ba72c5f8f2e39609263329171c4e9578.png)
图 27:构建 AmitWebApplication

项目“构建成功”，这可以在输出窗口中看到:

![appletfig28](img/9f309409eb73a562b8c5f21261b3820c.png)
图 28:构建成功

现在，如上所示建立项目后，通过使用功能键 F6 或如下所示运行项目:

![appletfig29](img/f490d7c39cc6a3ec330eeb36c94503d7.png)
图 29:运行项目

该项目将在本地主机上成功运行，即在谷歌浏览器(你可以考虑任何其他浏览器)的下列地址:

```
localhost:8080/AmitWebApplication/index.jsp
```

现在，当小程序未签名时，将出现如下所示的安全警告:

> “Oracle 在 2012 年 12 月 11 日发布 Java 7 Update 10 时，为其 Java web 浏览器插件引入了新的安全特性”。
> 
> “像这样运行未签名的应用程序将在未来的版本中被阻止，因为它有潜在的不安全和安全风险”。

为了理解这些安全问题，我们需要澄清与“Java 安全模型”相关的概念，包括“未签名的 applet”、“已签名的 applet”和“自签名的 applet:

**未签名的小程序:**这种小程序不能访问所有系统属性、本地文件系统等。这缺乏安全性，因为小程序没有签名。根据“Java 7 Update 10”(如上所述)，用户在使用之前会收到警告。

**签名小程序:**在这种情况下，小程序由证书签名，浏览器使用证书授权服务器验证该证书。在成功验证签名和用户批准后，这种小应用程序获得更多的权限。与自签名(下面讨论)和未签名的小程序(上面讨论)相比，它可以做更多的任务。

**自签名小程序:**这些小程序由开发者签名，也可以被认为是安全问题。Java 插件仍然会在运行自签名小程序时发出警告，因为只有开发人员才能保证这些小程序的安全性。

要获得更多关于上述 Applet 安全模型相关概念的信息，请参考以下链接。不同之处在下面给出的链接中有详细的解释:http://www . mendoweb . be/blog/Java-applets-unsigned-vs-self-signed-vs-signed/

要运行当前项目“AmitWebApplication”，只需在选择“**我接受风险并希望运行此应用**”后，点击“**运行**”。这一步将成功运行您的小程序:

![appletfig30](img/9c9cb0008800b930c015ecd1de1d9061.png)
图 30:选择框“我接受风险并希望运行此应用程序”

这里可以看到来自 **AmitJavaApplication** 的小程序，这样我们就可以在网页上展示我们的小程序了。通过更改`applet`标签，可以在“index.jsp”中更改 Applet 的大小(高度、宽度):

```
<applet code="tutorial.me.AmitApplet" archive="AmitJavaApplication.jar" width="400" height="350">
</applet>
```

![appletfig31](img/69376503f05b3d7b5a550e087aff84b1.png)
图 31: Applet 在 JSP 页面上成功运行

## 结论

通过这个简单的教程，你应该对在 NetBeansIDE 中运行程序有所了解。我的目标是帮助 NetBeansIDE 用户学习以下功能:

如何开始使用 NetBeansIDE
如何在 NetBeans IDE 中运行 Applet
了解 Applet 安全模型
如何在 NetBeans IDE 中运行 Web 应用程序
在 JSP 页面(Web 应用程序)中嵌入/集成您的 Applet。

## 资源

这些资源可以进一步帮助读者:

[NetBeansIDE](https://netbeans.org/)
[Java](http://www.java.com/en/)
[JSP](http://www.oracle.com/technetwork/java/jsp-138432.html)

你可以[在这里](https://uploads.sitepoint.com/wp-content/uploads/2013/11/project.zip)下载小程序和 Web 应用项目。

我希望你从这篇教程中学到了什么。期待你下面的评论。

## 分享这篇文章