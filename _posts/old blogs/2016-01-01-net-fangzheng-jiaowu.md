---
layout: post
title: 以前写的方正教务网客户端,有利于了解http协议
categories: others
---

**- 项目效果:**
-----------

 - 1.先检查动态码和viewstate码,至于为什么,稍后解释
 - ![先检查动态码,viewstate码](http://img.blog.csdn.net/20150521092134291)
 - 2.检查后发现如下(viewstate码已转换,特殊字符转换成了ascii)
 - ![这里写图片描述](http://img.blog.csdn.net/20150521092254694)
 - 3.这是为转换之前和转换之后的(为什么转换稍后解释)
 - ![这里写图片描述](http://img.blog.csdn.net/20150521092503023)
 - 4.登录成功
 - ![这里写图片描述](http://img.blog.csdn.net/20150521092546220)
 - 5.进入个人中心前会判断最近是否有考试
 - ![这里写图片描述](http://img.blog.csdn.net/20150521092602846)
 - 6.进入后自动弹出最近的考试
 - ![这里写图片描述](http://img.blog.csdn.net/20150521092642485)
 - 7.课表信息
 - ![这里写图片描述](http://img.blog.csdn.net/20150521092858521)
 - 8.个人中心
 - ![这里写图片描述](http://img.blog.csdn.net/20150521093008690)



项目问题分析
------
1.关于验证码:
	通过研究发现,输入教务网的地址http://jw1.hustwenhua.net/后会返回一个地址:http://jw1.hustwenhua.net/(syufyjbvpsbm2055i4odbmrh)/default2.aspx,有没有发现中间多了一串动态码?这个码是随机的,第一步是需要获得这个验证码,怎么做到呢?我是通过先用webbrowser访问默认地址,这时候访问后就会返回一个带有动态码的地址,只需要调用获取webbrowser的地址方法,就可以获取到这个地址,下一步就是截取这个动态码了:
	

```
 string urldefault = webBrowser1.Url.ToString();
            globals.urlDefault = urldefault;

            //获得浏览器地址栏的动态码
            string first = urldefault.Substring(urldefault.LastIndexOf('(')+1);
            string last = first.Substring(0, first.LastIndexOf(')'));

            //截取到动态码,保存到globals里的dynamiccode中
            globals.dynamicCode = last;
```
以上有globals类,这个类是我建立的一个通用类,在众多窗体之间实现变量的互相访问,这个在这个程序里很重要,因为post,get请求等需要很多相关的信息.上面的一部,我就把动态码保存在了 dynamiccode里了,往后的HttpRequest都需要用到.
2.关于viewstate.在登录post请求的时候需要用到,这个码是在返回默认主页的时候页面信息里自带的,下面就是我用正则表达式截取viewstate然后做相关处理的代码:
首先是抓包 post请求:
请求标头:
![这里写图片描述](http://img.blog.csdn.net/20150521094124729)
请求正文:
![这里写图片描述](http://img.blog.csdn.net/20150521094224696)
其中马赛克部分是密码...
抓取实现代码:

```
 string strHTML = "";
            WebClient myWebClient = new WebClient();
            Stream myStream = myWebClient.OpenRead("http://jw1.hustwenhua.net/" + "(" + globals.dynamicCode + ")" + "/" + "default2.aspx");
            StreamReader sr = new StreamReader(myStream, System.Text.Encoding.GetEncoding("utf-8"));
            strHTML = sr.ReadToEnd();
            myStream.Close();
            string temp;
            //获取viewstate码
            Regex my=new Regex(@"<input[^>]*name=\""__VIEWSTATE\""[^>]*value=\""([^""]*)\""[^>]*>");
            MatchCollection mymatch=my.Matches(strHTML);
              foreach (Match m in mymatch)
                {
                    
                       globals.viewstate=m.ToString();
                   }
              temp = globals.viewstate;

            //获取gnmkd码:


            globals.viewstate = globals.viewstate.Substring(globals.viewstate.LastIndexOf("value=") + 7);
            globals.viewstate = globals.viewstate.Substring(0,globals.viewstate.LastIndexOf("\""));
            globals.viewstatedefault = globals.viewstate;

            //因为服务器不能识别某些特殊字符,需要转换为Ascii码
            globals.viewstate = globals.viewstate.Replace("/", "%2F");
            globals.viewstate = globals.viewstate.Replace("+", "%2B");
            globals.viewstate = globals.viewstate.Replace("=", "%3D");
```
服务器有的特殊字符是不能识别的,下面是常见的:

```
		//1. + URL 中+号表示空格 %2B 
        //2. 空格 URL中的空格可以用+号或者编码 %20 
        //3. / 分隔目录和子目录 %2F 
        //4. ? 分隔实际的 URL 和参数 %3F 
        //5. % 指定特殊字符 %25 
        //6. # 表示书签 %23 
        //7. & URL 中指定的参数间的分隔符 %26 
        //8. = URL 中指定参数的值 %3D 
```
3.做好了准备工作,就要准备Post数据,Post地址了,下面是我准备的:

posturl:
```
   globals.postUrl = "http://jw1.hustwenhua.net/" + "(" + globals.dynamicCode + ")" + "/" + "default2.aspx";

```
上面的dynamiccode之前已经说过,是抓好的动态码

postdata:

```
 globals.postData = "__VIEWSTATE=" + globals.viewstate + "&txtUserName=" + userNameTextBox.Text.ToString() + "&TextBox2=" + PassWordTextBox.Text.ToString() + "&txtSecretCode=" + secretCodeTextBox.Text.ToString() + "&RadioButtonList1=" + globals.userLevel + "&Button1=&lbLanguage=&hidPdrs=&hidsc=";
```
上面是把抓包获取的请求正文通过处理使它成为通用的.
其中的意义:
viewstate:之前已抓过的码
username:学号
password:密码
secretcode:验证码
userlevel:这是用户级别,通过抓包获取的
//学生:%D1%A7%C9%FA
        //老师:%BD%CC%CA%A6
        //部门:%B2%BF%C3%C5
        //访客无意义
后面的是没有值的,每次抓包都不变.
4.开始登录了:
首先获取验证码,验证码的地址:

```
 globals.picUrl = "http://jw1.hustwenhua.net/" + "(" + globals.dynamicCode + ")" + "/" + "CheckCode.aspx";
```
登录方法(Post请求):

```
private string Login(string Url, string postDataStr)
        {

            HttpWebRequest request = (HttpWebRequest)WebRequest.Create(Url);

            request.Method = "POST";

            request.ContentType = "application/x-www-form-urlencoded";

            request.ContentLength = Encoding.UTF8.GetByteCount(postDataStr);

            Stream myRequestStream = request.GetRequestStream();

            StreamWriter myStreamWriter = new StreamWriter(myRequestStream, Encoding.GetEncoding("gb2312"));

            myStreamWriter.Write(postDataStr);

            myStreamWriter.Close();

            HttpWebResponse response = (HttpWebResponse)request.GetResponse();
            Stream myResponseStream = response.GetResponseStream();

            StreamReader myStreamReader = new StreamReader(myResponseStream, Encoding.GetEncoding("gb2312"));

            string retString = myStreamReader.ReadToEnd();

            myStreamReader.Close();

            myResponseStream.Close();

            return retString;

        }
```
个人图片请求:

```
 private void GetImagePersonPic(string url)
        {

            //            CookieContainer cookies = new CookieContainer();
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create(url);
            request.Accept = "*/*";
            request.Method = "GET";
            request.UserAgent = "Mozilla/5.0";

            request.CookieContainer = new CookieContainer();
            HttpWebResponse response = (HttpWebResponse)request.GetResponse();
            MemoryStream ms = null;
            using (var stream = response.GetResponseStream())
            {
                Byte[] buffer = new Byte[response.ContentLength];
                int offset = 0, actuallyRead = 0;
                do
                {
                    actuallyRead = stream.Read(buffer, offset, buffer.Length - offset);
                    offset += actuallyRead;
                } while (actuallyRead > 0);
                ms = new MemoryStream(buffer);
            }
            response.Close();
            //            globals.cookies = request.CookieContainer;
            //            globals.strCookies = request.CookieContainer.GetCookieHeader(request.RequestUri);
            try
            {
                Image image = new Bitmap(ms, true);
                globals.personImage = image;
                pictureBox2.Image = image;
            }catch(Exception e)
            {
                MessageBox.Show("密码错误!");
                this.Hide();
                LoginForm frm = new LoginForm();
                frm.Show();

            }
            
        }
```
验证码请求:

```
 private void GetImage(string url)
        {
            
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create(url);
            request.Accept = "*/*";
            request.Method = "GET";
            request.UserAgent = "Mozilla/5.0";
//            request.CookieContainer = new CookieContainer();
            HttpWebResponse response = (HttpWebResponse)request.GetResponse();
            MemoryStream ms = null;
            using (var stream = response.GetResponseStream())
            {
                Byte[] buffer = new Byte[response.ContentLength];
                int offset = 0, actuallyRead = 0;
                do
                {
                    actuallyRead = stream.Read(buffer, offset, buffer.Length - offset);
                    offset += actuallyRead;
                } while (actuallyRead > 0);
                ms = new MemoryStream(buffer);
            }
            response.Close();
            Image image = new Bitmap(ms, true);
            pictureBox1.Image = image;
        }
```
登录上去了之后,又发现了一些问题

5.又发现的问题:
脚本错误,怎么忽略脚本错误呢?
加上如下代码:
webBrowser1.ScriptErrorsSuppressed = true;
发现的问题:
get请求后返回的是登录页面,原来是需要申明请求头,即Referer,没有的话相当于非法访问.
发现的问题:
查询成绩我一直在想,学校的微信平台是怎么做到的,我每次抓包,得到的post请求正文足足有137KB,真是够麻烦的,这个真心难分析.暂时先放一下,等日后有了灵感知道怎么分析了再来抓取成绩,现在除了成绩,其他的基本都可以获取了,很方便.

**

项目前景:
-----
想做成移动客户端,方便学生使用,附带新闻,通知等各种功能,考试通知,考试查询,上课通知等等.这个有一定的挑战性,因为要考虑整个学校学生的通用性.

**
**

项目主要代码:
-----
PrepareForm.cs:

```
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;
using System.Text.RegularExpressions;
using System.Net;
using System.IO; //正则表达式

namespace spider_1
{
    public partial class PrepareForm : Form
    {
        
        //通过百度找到的一下特殊符号对应的Ascii码

        //1. + URL 中+号表示空格 %2B 
        //2. 空格 URL中的空格可以用+号或者编码 %20 
        //3. / 分隔目录和子目录 %2F 
        //4. ? 分隔实际的 URL 和参数 %3F 
        //5. % 指定特殊字符 %25 
        //6. # 表示书签 %23 
        //7. & URL 中指定的参数间的分隔符 %26 
        //8. = URL 中指定参数的值 %3D 

        public PrepareForm()
        {
            InitializeComponent();
        }
        
        //预处理就是获取动态码了
        private void prepare_Load(object sender, EventArgs e)
        {
            webBrowser1.Navigate("http://jw1.hustwenhua.net");
        }
        private void webBrowser1_DocumentCompleted(object sender, WebBrowserDocumentCompletedEventArgs e)
        {
        }


        //获取到动态码后来获取viewstate,这时候还没有经过处理
        private void button1_Click(object sender, EventArgs e)
        {
            //默认的登录地址

            string urldefault = webBrowser1.Url.ToString();
            globals.urlDefault = urldefault;

            //获得浏览器地址栏的动态码
            string first = urldefault.Substring(urldefault.LastIndexOf('(')+1);
            string last = first.Substring(0, first.LastIndexOf(')'));

            //截取到动态码,保存到globals里的dynamiccode中
            globals.dynamicCode = last;

            //创建一个字符串来保存主页的信息用来截取字符串
            string strHTML = "";
            WebClient myWebClient = new WebClient();
            Stream myStream = myWebClient.OpenRead("http://jw1.hustwenhua.net/" + "(" + globals.dynamicCode + ")" + "/" + "default2.aspx");
            StreamReader sr = new StreamReader(myStream, System.Text.Encoding.GetEncoding("utf-8"));
            strHTML = sr.ReadToEnd();
            myStream.Close();
            string temp;
            //获取viewstate码
            Regex my=new Regex(@"<input[^>]*name=\""__VIEWSTATE\""[^>]*value=\""([^""]*)\""[^>]*>");
            MatchCollection mymatch=my.Matches(strHTML);
              foreach (Match m in mymatch)
                {
                    
                       globals.viewstate=m.ToString();
                   }
              temp = globals.viewstate;

            //获取gnmkd码:


            globals.viewstate = globals.viewstate.Substring(globals.viewstate.LastIndexOf("value=") + 7);
            globals.viewstate = globals.viewstate.Substring(0,globals.viewstate.LastIndexOf("\""));
            globals.viewstatedefault = globals.viewstate;

            //因为服务器不能识别某些特殊字符,需要转换为Ascii码
            globals.viewstate = globals.viewstate.Replace("/", "%2F");
            globals.viewstate = globals.viewstate.Replace("+", "%2B");
            globals.viewstate = globals.viewstate.Replace("=", "%3D");



                globals.postUrl = "http://jw1.hustwenhua.net/" + "(" + globals.dynamicCode + ")" + "/" + "default2.aspx";
            //在信息栏显示信息
            richTextBox1.Text += "\n\n" + "动态码:\n\n" + globals.dynamicCode + "\n\nviewState码:\n\n" + globals.viewstate + "\n\n" + "已完成必须的步骤\n\n"+"请点击完成按钮继续下一步操作";
            globals.picUrl = "http://jw1.hustwenhua.net/" + "(" + globals.dynamicCode + ")" + "/" + "CheckCode.aspx";
            richTextBox1.Text += "\n\n" + globals.viewstate1 + "\n\n"+globals.viewstate2 +"\n\n"+globals.viewstate3;

            richTextBox1.Text += "\n\n处理前的viewstate:" + globals.viewstatedefault;
            richTextBox1.Text += "\n\n处理后的viewstate:" + globals.viewstate;
        }

        private void button2_Click(object sender, EventArgs e)
        {
            this.Hide();
            LoginForm frm1 = new LoginForm();
            frm1.Show();
        }
    }
}

```
LoginForm.cs:

```
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.IO;
using System.Linq;
using System.Net;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;

namespace spider_1
{
    public partial class LoginForm : Form
    {
        //学生:%D1%A7%C9%FA
        //老师:%BD%CC%CA%A6
        //部门:%B2%BF%C3%C5
        //访客无意义

        public LoginForm()
        {
            InitializeComponent();
        }

        //登录Post方法
        private string Login(string Url, string postDataStr)
        {

            HttpWebRequest request = (HttpWebRequest)WebRequest.Create(Url);

            request.Method = "POST";

            request.ContentType = "application/x-www-form-urlencoded";

            request.ContentLength = Encoding.UTF8.GetByteCount(postDataStr);

            Stream myRequestStream = request.GetRequestStream();

            StreamWriter myStreamWriter = new StreamWriter(myRequestStream, Encoding.GetEncoding("gb2312"));

            myStreamWriter.Write(postDataStr);

            myStreamWriter.Close();

            HttpWebResponse response = (HttpWebResponse)request.GetResponse();
            Stream myResponseStream = response.GetResponseStream();

            StreamReader myStreamReader = new StreamReader(myResponseStream, Encoding.GetEncoding("gb2312"));

            string retString = myStreamReader.ReadToEnd();

            myStreamReader.Close();

            myResponseStream.Close();

            return retString;

        }


        private void GetImagePersonPic(string url)
        {

            //            CookieContainer cookies = new CookieContainer();
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create(url);
            request.Accept = "*/*";
            request.Method = "GET";
            request.UserAgent = "Mozilla/5.0";

            request.CookieContainer = new CookieContainer();
            HttpWebResponse response = (HttpWebResponse)request.GetResponse();
            MemoryStream ms = null;
            using (var stream = response.GetResponseStream())
            {
                Byte[] buffer = new Byte[response.ContentLength];
                int offset = 0, actuallyRead = 0;
                do
                {
                    actuallyRead = stream.Read(buffer, offset, buffer.Length - offset);
                    offset += actuallyRead;
                } while (actuallyRead > 0);
                ms = new MemoryStream(buffer);
            }
            response.Close();
            //            globals.cookies = request.CookieContainer;
            //            globals.strCookies = request.CookieContainer.GetCookieHeader(request.RequestUri);
            try
            {
                Image image = new Bitmap(ms, true);
                globals.personImage = image;
                pictureBox2.Image = image;
            }catch(Exception e)
            {
                MessageBox.Show("密码错误!");
                this.Hide();
                LoginForm frm = new LoginForm();
                frm.Show();

            }
            
        }

        //获取验证码方法
        private void GetImage(string url)
        {
            
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create(url);
            request.Accept = "*/*";
            request.Method = "GET";
            request.UserAgent = "Mozilla/5.0";
//            request.CookieContainer = new CookieContainer();
            HttpWebResponse response = (HttpWebResponse)request.GetResponse();
            MemoryStream ms = null;
            using (var stream = response.GetResponseStream())
            {
                Byte[] buffer = new Byte[response.ContentLength];
                int offset = 0, actuallyRead = 0;
                do
                {
                    actuallyRead = stream.Read(buffer, offset, buffer.Length - offset);
                    offset += actuallyRead;
                } while (actuallyRead > 0);
                ms = new MemoryStream(buffer);
            }
            response.Close();
            Image image = new Bitmap(ms, true);
            pictureBox1.Image = image;
        }

       //LoginForm : Load 获取验证码
        private void Form1_Load(object sender, EventArgs e)
        {
            radioButton1.Checked = true;
            GetImage(globals.picUrl);
        }

        //点击登录
       private void button2_Click(object sender, EventArgs e)
       {

           //将用户Id放到globals类中的userid中
           globals.userid = userNameTextBox.Text.ToString();

           //将个人头像信息的地址放到globals类中的personPicUrl变量中
           globals.personPicUrl = "http://jw1.hustwenhua.net/" + "(" + globals.dynamicCode + ")" + "/" + "readimagexs.aspx?xh=" + userNameTextBox.Text.ToString();

           //准备postData
           globals.postData = "__VIEWSTATE=" + globals.viewstate + "&txtUserName=" + userNameTextBox.Text.ToString() + "&TextBox2=" + PassWordTextBox.Text.ToString() + "&txtSecretCode=" + secretCodeTextBox.Text.ToString() + "&RadioButtonList1=" + globals.userLevel + "&Button1=&lbLanguage=&hidPdrs=&hidsc=";

           //调用Login方法,将返回的主业信息放到IndexHtml里面
              globals.indexHtml = Login(globals.urlDefault, globals.postData);
              
           //展示个人照片
              GetImagePersonPic(globals.personPicUrl);


           //获取个人信息
              //获取学生的姓名
              string temp = globals.indexHtml;
              string first = temp.Substring(temp.LastIndexOf("\"xhxm\">") + 7);
              string last = first.Substring(0, first.LastIndexOf("同学"));
              globals.username = last;

           //提示登录成功

              this.Text = "登录成功,欢迎您:"+globals.username;

       }
  
        //老师按钮
      private void radioButton2_CheckedChanged(object sender, EventArgs e)
      {
          globals.userLevel = "%BD%CC%CA%A6";
      }
        //部门按钮
      private void radioButton3_CheckedChanged(object sender, EventArgs e)
      {
          globals.userLevel = "%B2%BF%C3%C5";
      }

      private void label4_Click(object sender, EventArgs e)
      {
          GetImage(globals.picUrl);
      }

      private void button1_Click(object sender, EventArgs e)
      {
          this.Hide();
          MainForm mfrm = new MainForm();
          mfrm.Show();
      }



    }
}


```
MainForm.cs:

```
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.IO;
using System.Linq;
using System.Net;
using System.Text;
using System.Text.RegularExpressions;
using System.Threading.Tasks;
using System.Windows.Forms;

namespace spider_1
{
    public partial class MainForm : Form
    {
        public MainForm()
        {
            InitializeComponent();
        }


        //做一些预处理的工作
        private void MainForm_Load(object sender, EventArgs e)
        {
            globals.KS = GetWeb("http://jw1.hustwenhua.net/("+globals.dynamicCode+")/xskscx.aspx?xh=" + globals.userid + "&xm=" + globals.username + "&gnmkdm=N121602");
            if(globals.KS!=null)
            {
                MessageBox.Show("最近有考试哦!,请查看", "考试安排");
                KS ks1 = new KS();
                ks1.Show();
            }
           
            webBrowser1.Navigate("http://jw1.hustwenhua.net/(" + globals.dynamicCode + ")/xs_main.aspx?xh=" + globals.userid);
            //获取学生的姓名
            string temp=globals.indexHtml;
            string first = temp.Substring(temp.LastIndexOf("\"xhxm\">") +7 );
            string last = first.Substring(0, first.LastIndexOf("同学"));
            label1.Text += last;
            globals.username=last;


        }

        private string KBCX(string Url)
        {

            HttpWebRequest request = (HttpWebRequest)WebRequest.Create(Url);
            request.Method = "GET";

            //先前存放的Cookies
            //request.CookieContainer = globals.responseCookies;
            HttpWebResponse response = (HttpWebResponse)request.GetResponse();
            StreamReader myreader = new StreamReader(response.GetResponseStream(), Encoding.UTF8);
            string responseText = myreader.ReadToEnd();
            return responseText;

        }
        private void button1_Click(object sender, EventArgs e)
        {
            /*globals.urlCXKB = "http://jw1.hustwenhua.net/" + "(" + globals.dynamicCode + ")" + "/xskbcx.aspx?xh=" + globals.userid + "&xm=" + globals.username + "&gnmkdm=N121602";
            webBrowser1.DocumentText = GetWeb(globals.urlCXKB);*/
          /*  string postData = "__EVENTTARGET=&__EVENTARGUMENT=&__VIEWSTATE=dDwtMzg4NjMzNTA5O3Q8cDxsPFNvcnRFeHByZXM7c2ZkY2JrO2RnMztkeWJ5c2NqO1NvcnREaXJlO3hoO3N0cl90YWJfYmpnO2NqY3hfbHNiO3p4Y2pjeHhzOz47bDxrY21jO1xlO2JqZzsxO2FzYzsxMjAxMDUwMzExMDQ7emZfY3hjanRqXzEyMDEwNTAzMTEwNDs7MTs%2BPjtsPGk8MT47PjtsPHQ8O2w8aTw0PjtpPDEwPjtpPDE5PjtpPDI0PjtpPDMyPjtpPDM0PjtpPDM2PjtpPDM4PjtpPDQwPjtpPDQyPjtpPDQ0PjtpPDQ2PjtpPDQ4PjtpPDUyPjtpPDU0PjtpPDU2Pjs%2BO2w8dDx0PHA8cDxsPERhdGFUZXh0RmllbGQ7RGF0YVZhbHVlRmllbGQ7PjtsPFhOO1hOOz4%2BOz47dDxpPDE2PjtAPFxlOzIwMTUtMjAxNjsyMDE0LTIwMTU7MjAxMy0yMDIxOzIwMTMtMjAyMDsyMDEzLTIwMTk7MjAxMy0yMDE4OzIwMTMtMjAxNzsyMDEzLTIwMTY7MjAxMy0yMDE1OzIwMTMtMjAxNDsyMDEyLTIwMTM7MjAxMS0yMDEyOzIwMTAtMjAxMTsyMDA5LTIwMTA7MjAwOC0yMDA5Oz47QDxcZTsyMDE1LTIwMTY7MjAxNC0yMDE1OzIwMTMtMjAyMTsyMDEzLTIwMjA7MjAxMy0yMDE5OzIwMTMtMjAxODsyMDEzLTIwMTc7MjAxMy0yMDE2OzIwMTMtMjAxNTsyMDEzLTIwMTQ7MjAxMi0yMDEzOzIwMTEtMjAxMjsyMDEwLTIwMTE7MjAwOS0yMDEwOzIwMDgtMjAwOTs%2BPjs%2BOzs%2BO3Q8dDxwPHA8bDxEYXRhVGV4dEZpZWxkO0RhdGFWYWx1ZUZpZWxkOz47bDxrY3h6bWM7a2N4emRtOz4%2BOz47dDxpPDIwPjtAPOWFrOWFseWfuuehgOivvjvkuJPkuJrmoLjlv4Por7476YCa6K%2BG5b%2BF5L%2Bu6K%2B%2BO%2BS4k%2BS4muaKgOiDveivvjvpm4bkuK3lrp7ot7XmlZnlraY75q%2BV5Lia6K6%2B6K6hKOiuuuaWhyk76Leo5LiT5Lia6YCJ5L%2Bu6K%2B%2BO%2BWFrOWFsemAieS%2Fruivvjvlhbbku5bor77nqIs75Y%2Bw5rm%2B5Lqk5rWB5a2m5LmgO%2Be9kee7nOmAieS%2FruivvjvmgJ3mlL%2Flrp7ot7Xor7475LiT5Lia5Z%2B656GA6K%2B%2BO%2BS4k%2BS4muivvjvkuJPkuJrpgInkv67or7475a6e6Le16K%2B%2BO%2BS4k%2BS4muaWueWQkeivvjvlrp7ot7Xnjq%2FoioI75LiT5Lia5o%2BQ6auY6K%2B%2BO1xlOz47QDwxOzEwOzExOzEzOzE0OzE3OzE5OzI7MjI7MjM7MjQ7MjU7Mzs0OzU7Njs3Ozg7OTtcZTs%2BPjs%2BOzs%2BO3Q8cDxwPGw8VmlzaWJsZTs%2BO2w8bzxmPjs%2BPjs%2BOzs%2BO3Q8cDxwPGw8VmlzaWJsZTs%2BO2w8bzxmPjs%2BPjs%2BOzs%2BO3Q8cDxwPGw8VmlzaWJsZTs%2BO2w8bzxmPjs%2BPjs%2BOzs%2BO3Q8cDxwPGw8VGV4dDs%2BO2w8XGU7Pj47Pjs7Pjt0PHA8cDxsPFRleHQ7VmlzaWJsZTs%2BO2w85a2m5Y%2B377yaMTIwMTA1MDMxMTA0O288dD47Pj47Pjs7Pjt0PHA8cDxsPFRleHQ7VmlzaWJsZTs%2BO2w85aeT5ZCN77ya5r2Y5bCaO288dD47Pj47Pjs7Pjt0PHA8cDxsPFRleHQ7VmlzaWJsZTs%2BO2w85a2m6Zmi77ya5L%2Bh5oGv56eR5a2m5LiO5oqA5pyv5a2m6YOoO288dD47Pj47Pjs7Pjt0PHA8cDxsPFRleHQ7VmlzaWJsZTs%2BO2w85LiT5Lia77yaO288dD47Pj47Pjs7Pjt0PHA8cDxsPFRleHQ7VmlzaWJsZTs%2BO2w86L2v5Lu25bel56iLO288dD47Pj47Pjs7Pjt0PHA8cDxsPFRleHQ7PjtsPOS4k%2BS4muaWueWQkTo7Pj47Pjs7Pjt0PHA8cDxsPFRleHQ7VmlzaWJsZTs%2BO2w86KGM5pS%2F54%2Bt77ya6L2v5Lu2MTIzO288dD47Pj47Pjs7Pjt0PEAwPHA8cDxsPFZpc2libGU7PjtsPG88Zj47Pj47Pjs7Ozs7Ozs7Ozs%2BOzs%2BO3Q8O2w8aTwxPjtpPDM%2BO2k8NT47aTw3PjtpPDk%2BO2k8MTE%2BO2k8MTM%2BO2k8MTU%2BO2k8MTc%2BO2k8MTg%2BO2k8MTk%2BO2k8MjE%2BO2k8MjM%2BO2k8MjU%2BO2k8Mjc%2BO2k8Mjk%2BO2k8Mzc%2BO2k8NDM%2BO2k8NDU%2BO2k8NDY%2BOz47bDx0PHA8cDxsPFZpc2libGU7PjtsPG88Zj47Pj47Pjs7Pjt0PEAwPHA8cDxsPFZpc2libGU7PjtsPG88Zj47Pj47cDxsPHN0eWxlOz47bDxESVNQTEFZOm5vbmU7Pj4%2BOzs7Ozs7Ozs7Oz47Oz47dDw7bDxpPDEzPjs%2BO2w8dDxAMDw7Ozs7Ozs7Ozs7Pjs7Pjs%2BPjt0PHA8cDxsPFRleHQ7VmlzaWJsZTs%2BO2w86Iez5LuK5pyq6YCa6L%2BH6K%2B%2B56iL5oiQ57up77yaO288dD47Pj47Pjs7Pjt0PEAwPHA8cDxsPFBhZ2VDb3VudDtfIUl0ZW1Db3VudDtfIURhdGFTb3VyY2VJdGVtQ291bnQ7RGF0YUtleXM7PjtsPGk8MT47aTwxPjtpPDE%2BO2w8Pjs%2BPjtwPGw8c3R5bGU7PjtsPERJU1BMQVk6YmxvY2s7Pj4%2BOzs7Ozs7Ozs7Oz47bDxpPDA%2BOz47bDx0PDtsPGk8MT47PjtsPHQ8O2w8aTwwPjtpPDE%2BO2k8Mj47aTwzPjtpPDQ%2BO2k8NT47PjtsPHQ8cDxwPGw8VGV4dDs%2BO2w8ajA4NTAzMTs%2BPjs%2BOzs%2BO3Q8cDxwPGw8VGV4dDs%2BO2w8572R57uc6K6k6K%2BB77yIQ0NOQS9IM0Mv6L2v6ICD77yJ5Y%2BK572R57uc56ue6LWb5Z%2B56K6tOz4%2BOz47Oz47dDxwPHA8bDxUZXh0Oz47bDzlhazlhbHpgInkv67or747Pj47Pjs7Pjt0PHA8cDxsPFRleHQ7PjtsPDIuMDs%2BPjs%2BOzs%2BO3Q8cDxwPGw8VGV4dDs%2BO2w8MDs%2BPjs%2BOzs%2BO3Q8cDxwPGw8VGV4dDs%2BO2w85YWs6YCJ6K%2B%2BOz4%2BOz47Oz47Pj47Pj47Pj47dDxAMDxwPHA8bDxWaXNpYmxlOz47bDxvPGY%2BOz4%2BO3A8bDxzdHlsZTs%2BO2w8RElTUExBWTpub25lOz4%2BPjs7Ozs7Ozs7Ozs%2BOzs%2BO3Q8QDA8cDxwPGw8VmlzaWJsZTs%2BO2w8bzxmPjs%2BPjtwPGw8c3R5bGU7PjtsPERJU1BMQVk6bm9uZTs%2BPj47Ozs7Ozs7Ozs7Pjs7Pjt0PEAwPDs7Ozs7Ozs7Ozs%2BOzs%2BO3Q8QDA8cDxwPGw8VmlzaWJsZTs%2BO2w8bzxmPjs%2BPjtwPGw8c3R5bGU7PjtsPERJU1BMQVk6bm9uZTs%2BPj47Ozs7Ozs7Ozs7Pjs7Pjt0PEAwPHA8cDxsPFZpc2libGU7PjtsPG88Zj47Pj47cDxsPHN0eWxlOz47bDxESVNQTEFZOm5vbmU7Pj4%2BOzs7Ozs7Ozs7Oz47Oz47dDxAMDxwPHA8bDxWaXNpYmxlOz47bDxvPGY%2BOz4%2BOz47Ozs7Ozs7Ozs7Pjs7Pjt0PEAwPHA8cDxsPFZpc2libGU7PjtsPG88Zj47Pj47cDxsPHN0eWxlOz47bDxESVNQTEFZOm5vbmU7Pj4%2BOzs7Ozs7Ozs7Oz47Oz47dDxAMDxwPHA8bDxWaXNpYmxlOz47bDxvPGY%2BOz4%2BO3A8bDxzdHlsZTs%2BO2w8RElTUExBWTpub25lOz4%2BPjs7Ozs7Ozs7Ozs%2BOzs%2BO3Q8QDA8O0AwPDs7QDA8cDxsPEhlYWRlclRleHQ7PjtsPOWIm%2BaWsOWGheWuuTs%2BPjs7Ozs%2BO0AwPHA8bDxIZWFkZXJUZXh0Oz47bDzliJvmlrDlrabliIY7Pj47Ozs7PjtAMDxwPGw8SGVhZGVyVGV4dDs%2BO2w85Yib5paw5qyh5pWwOz4%2BOzs7Oz47Ozs%2BOzs7Ozs7Ozs7Pjs7Pjt0PHA8cDxsPFRleHQ7VmlzaWJsZTs%2BO2w85pys5LiT5Lia5YWxODfkuro7bzxmPjs%2BPjs%2BOzs%2BO3Q8cDxwPGw8VmlzaWJsZTs%2BO2w8bzxmPjs%2BPjs%2BOzs%2BO3Q8cDxwPGw8VmlzaWJsZTs%2BO2w8bzxmPjs%2BPjs%2BOzs%2BO3Q8cDxwPGw8VmlzaWJsZTs%2BO2w8bzxmPjs%2BPjs%2BOzs%2BO3Q8cDxwPGw8VGV4dDs%2BO2w8SEhYWTs%";*/
            

        }

        //Get方式获取网页数据
        private string GetWeb(string url)
        {
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create(url);
            request.Method = "GET";
            request.Referer = "http://jw1.hustwenhua.net/("+globals.dynamicCode+"/xs_main.aspx?xh="+globals.userid;
            //先前存放的Cookies
            //request.CookieContainer = globals.responseCookies;
            HttpWebResponse response = (HttpWebResponse)request.GetResponse();
            StreamReader myreader = new StreamReader(response.GetResponseStream(),Encoding.GetEncoding("gb2312"));
            string responseText = myreader.ReadToEnd();
            return responseText;
        }

        private void label1_Click(object sender, EventArgs e)
        {
        }

        private void button1_Click_1(object sender, EventArgs e)
        {
            globals.KB = GetWeb("http://jw1.hustwenhua.net/("+globals.dynamicCode+")/xskbcx.aspx?xh=" + globals.userid + "&xm=" + globals.username + "&gnmkdm=N121602");
            KB kb = new KB();
            kb.Show();
        }

        private void button2_Click(object sender, EventArgs e)
        {
            globals.KS = GetWeb("http://jw1.hustwenhua.net/("+globals.dynamicCode+")/xskscx.aspx?xh=" + globals.userid + "&xm=" + globals.username + "&gnmkdm=N121603");
            KS ks = new KS();
            ks.Show();
        }

        private void button3_Click(object sender, EventArgs e)
        {
            globals.KB = GetWeb("http://jw1.hustwenhua.net/("+globals.dynamicCode+")/xsbkkscx.aspx?xh=" + globals.userid + "&xm=" + globals.username + "&gnmkdm=N121604");
            BK bk = new BK();
            bk.Show();
        }

        private void button4_Click(object sender, EventArgs e)
        {
            notify1 frm = new notify1();
            notify2 frm2 = new notify2();
            frm.Show();
            frm2.Show();
        }

        private void button5_Click(object sender, EventArgs e)
        {
            PersonPic pp = new PersonPic();
            pp.Show();
        }

        private void button6_Click(object sender, EventArgs e)
        {
            globals.PYJH = GetWeb("http://jw1.hustwenhua.net/("+globals.dynamicCode+")/pyjh.aspx?xh=" + globals.userid + "&xm=" + globals.username + "&gnmkdm=N121606");
            PYJH pyjh = new PYJH();
            pyjh.Show();
        }
      
    }
}

```
KB.cs:

```
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;

namespace spider_1
{
    public partial class KB : Form
    {
        public KB()
        {
            InitializeComponent();
        }

        private void KB_Load(object sender, EventArgs e)
        {
            webBrowser1.ScriptErrorsSuppressed = true;
            webBrowser1.DocumentText = globals.KB;
        }
    }
}

```
KS.cs:

```
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;

namespace spider_1
{
    public partial class KS : Form
    {
        public KS()
        {
            InitializeComponent();
        }

        private void KS_Load(object sender, EventArgs e)
        {
            webBrowser1.ScriptErrorsSuppressed = true;
            webBrowser1.DocumentText = globals.KS;
            
        }
    }
}

```
BK.cs

```
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;

namespace spider_1
{
    public partial class BK : Form
    {
        public BK()
        {
            InitializeComponent();
        }

        private void BK_Load(object sender, EventArgs e)
        {
            webBrowser1.ScriptErrorsSuppressed = true;
            webBrowser1.DocumentText = globals.BK;
        }
    }
}

```
notify.cs

```
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;

namespace spider_1
{
    public partial class notify1 : Form
    {
        public notify1()
        {
            InitializeComponent();
        }

        private void notify1_Load(object sender, EventArgs e)
        {
            webBrowser1.ScriptErrorsSuppressed = true;
            webBrowser1.Navigate("http://jw1.hustwenhua.net/("+globals.dynamicCode+")/ggsm.aspx?fbsj=2015-05-19 10:50:00&yxqx=2015-06-30&xh= ");
        }
    }
}

```
PersonPic.cs

```
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;

namespace spider_1
{
    public partial class PersonPic : Form
    {
        public PersonPic()
        {
            InitializeComponent();
        }

        private void PersonPic_Load(object sender, EventArgs e)
        {
            pictureBox1.Image = globals.personImage;
        }
    }
}

```
globals.cs:

```
using System;
using System.Collections.Generic;
using System.Drawing;
using System.Linq;
using System.Net;
using System.Text;
using System.Threading.Tasks;

namespace spider_1
{
    class globals
    {
        //通用类,所有的可以访问这些变量

        //存放个人头像
        public static Image personImage = null;
        //用不着,存放各类表查询对应的号码,通过抓包获取
        public static string gnkm = null;
        //学生课表的URL,之前的测试
        public static string urlCXKB;
        //默认的地址,登录地址
        public static string urlDefault;
        //动态码
        public static string dynamicCode;
        //viewstate码
        public static string viewstate;
        //这些是之前的测试
        public static string viewstate1;
        public static string viewstate2;
        public static string viewstate3;
        //默认的viewstate码
        public static string viewstatedefault;
        //登录用的地址
        public static string postUrl;
        //登录的请求正文
        public static string postData;
        //用户的级别
        public static string userLevel="%D1%A7%C9%FA";//默认为学生
        //登录后的主页
        public static string indexHtml;
        //个人头像的地址
        public static string personPicUrl;
        //用来测试
        public static string testUrl;
        //用户学号
        public static string userid;
        //用户姓名
        public static string username;
        //主页地址 用于 Get方法的 Referer
        public static string mainUrl;
        //验证码地址
        public static string picUrl;
        //测试用的Cookies
        public static string strCookies;
        //存放课表
        public static string KB;
        //存放考试
        public static string KS;
        //存放补考
        public static string BK;
        //存放培养计划
        public static string PYJH;
        //存放cookies
        public static CookieContainer requestCookies = null; //全局的request cookies
        public static CookieContainer responseCookies = null; //全局的response cookies
    }

}

```

**

项目源文件:
------
http://url.cn/chOTD3



