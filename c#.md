# 计时器

```c#
using System.Timers;


System.Timers.Timer pTimer = new System.Timers.Timer(1000);//每隔5秒执行一次，没用winfrom自带的
pTimer.Elapsed += pTimer_Elapsed;//委托，要执行的方法
pTimer.AutoReset = true;//获取该定时器自动执行
pTimer.Enabled = true;//这个一定要写，要不然定时器不会执行的
Control.CheckForIllegalCrossThreadCalls = false;//这个不太懂，有待研究
```


# pj


# 线程 委托 事件

```c#
class TestClass
{
        //声明一个delegate（委托）类型：testDelegate，该类型可以搭载返回值为空，参数只有一个(long型)的方法。
        public delegate void testDelegate(long i);

        //声明一个testDelegate类型的对象。该对象代表了返回值为空，参数只有一个(long型)的方法。它可以搭载N个方法。
        public testDelegate mainThread;

        /// <summary>
        /// 测试方法
        /// </summary>
        public void testFunction()
        {
            long i = 0;
            while(true)
            {
                i++;
                mainThread(i); //调用委托对象
                Thread.Sleep(1000);  //线程等待1000毫秒
            }
        }
}

```

```c#
[csharp] view plain copy print?
/// <summary>
/// 按钮单击事件
/// </summary>
/// <param name="sender"></param>
/// <param name="e"></param>
private void button1_Click(object sender, EventArgs e)
{
    //创建TestClass类的对象
    TestClass testclass = new TestClass();

    //在testclass对象的mainThread(委托)对象上搭载两个方法，在线程中调用mainThread对象时相当于调用了这两个方法。
    testclass.mainThread = new TestClass.testDelegate(refreshLabMessage1);
    testclass.mainThread += new TestClass.testDelegate(refreshLabMessage2);

    //创建一个无参数的线程,这个线程执行TestClass类中的testFunction方法。
    Thread testclassThread = new Thread(new ThreadStart(testclass.testFunction));
    //启动线程，启动之后线程才开始执行
    testclassThread.Start();
}

/// <summary>
/// 在界面上更新线程执行次数
/// </summary>
/// <param name="i"></param>
private void refreshLabMessage1(long i)
{
    //判断该方法是否被主线程调用，也就是创建labMessage1控件的线程，当控件的InvokeRequired属性为ture时，说明是被主线程以外的线程调用。如果不加判断，会造成异常
    if (this.labMessage1.InvokeRequired)
    {
        //再次创建一个TestClass类的对象
        TestClass testclass = new TestClass();
        //为新对象的mainThread对象搭载方法
        testclass.mainThread = new TestClass.testDelegate(refreshLabMessage1);
        //this指窗体，在这调用窗体的Invoke方法，也就是用窗体的创建线程来执行mainThread对象委托的方法，再加上需要的参数(i)
        this.Invoke(testclass.mainThread,new object[] {i});
    }
    else
    {
        labMessage1.Text = i.ToString();
    }
}

/// <summary>
/// 在界面上更新线程执行次数
/// </summary>
/// <param name="i"></param>
private void refreshLabMessage2(long i)
{
    //同上
    if (this.labMessage2.InvokeRequired)
    {
        //再次创建一个TestClass类的对象
        TestClass testclass = new TestClass();
        //为新对象的mainThread对象搭载方法
        testclass.mainThread = new TestClass.testDelegate(refreshLabMessage2);
        //this指窗体，在这调用窗体的Invoke方法，也就是用窗体的创建线程来执行mainThread对象委托的方法，再加上需要的参数(i)
        this.Invoke(testclass.mainThread, new object[] { i });
    }
    else
    {
        labMessage2.Text = i.ToString();
    }
}

```


# 控制声音
```C#
//使用示例
var config = ConfigFile.LoadOrCreateFile("userConfig.xml");

config.AddOrSetConfigValue("isOnline", false); //是否开机启动
config.AddOrSetConfigValue("isTop", false);  //窗口总在最上层
config.AddOrSetConfigValue("isAlarm", false); //报警
config.AddOrSetConfigValue("isSchema", false);    //允许 isSchema

//使用带参数的属性快速保存配置信息
config["string"] = "这是一条文本记录";
//使用带参数的属性快速保存非字符串
config["intX"] = 45.ToString();
config["Date"] = DateTime.Now.ToString();
//保存配置信息的一般方式
config.AddOrSetConfigValue("intY", 88);
config.AddOrSetConfigValue("bool", true);
//读取特定类型的数据
var x = config.GetConfigValueInt("intX");
//使用TryParse类方法读取特定类型的数据
int y;
config.TryParseConfigValue("intY", out y);
DateTime dt;
config.TryParseConfigValue("Date", out dt);
//使用GetConfigValueXXX方法读取特定类型的数据
bool b = config.GetConfigValueBool("bool");

```

```c#
using System;
using System.Runtime.InteropServices;

namespace OnePhone
{
    public class SoundControl
    {

        private NLog.Logger logger = NLog.LogManager.GetCurrentClassLogger();
        private const byte VK_VOLUME_MUTE = 0xAD;
        private const byte VK_VOLUME_DOWN = 0xAE;
        private const byte VK_VOLUME_UP = 0xAF;
        private const UInt32 KEYEVENTF_EXTENDEDKEY = 0x0001;
        private const UInt32 KEYEVENTF_KEYUP = 0x0002;

        [DllImport("user32.dll")]
        static extern void keybd_event(byte bVk, byte bScan, UInt32 dwFlags, UInt32 dwExtraInfo);

        [DllImport("user32.dll")]
        static extern Byte MapVirtualKey(UInt32 uCode, UInt32 uMapType);

        [DllImport("winmm.dll")]
        public static extern long waveOutSetVolume(UInt32 deviceID, UInt32 Volume);

        [DllImport("winmm.dll")]
        public static extern long waveOutGetVolume(UInt32 deviceID, out UInt32 Volume);

        /// <summary>
        /// 系统振铃器音量 [+]
        /// </summary>
        public static void VolumeUp()
        {
            keybd_event(VK_VOLUME_UP, MapVirtualKey(VK_VOLUME_UP, 0), KEYEVENTF_EXTENDEDKEY, 0);
            keybd_event(VK_VOLUME_UP, MapVirtualKey(VK_VOLUME_UP, 0), KEYEVENTF_EXTENDEDKEY | KEYEVENTF_KEYUP, 0);
        }

        /// <summary>
        /// 系统振铃器音量 [-]
        /// </summary>
        public static void VolumeDown()
        {
            keybd_event(VK_VOLUME_DOWN, MapVirtualKey(VK_VOLUME_DOWN, 0), KEYEVENTF_EXTENDEDKEY, 0);
            keybd_event(VK_VOLUME_DOWN, MapVirtualKey(VK_VOLUME_DOWN, 0), KEYEVENTF_EXTENDEDKEY | KEYEVENTF_KEYUP, 0);
        }

        /// <summary>
        /// 系统振铃器静音
        /// </summary>
        public static void Mute()
        {
            keybd_event(VK_VOLUME_MUTE, MapVirtualKey(VK_VOLUME_MUTE, 0), KEYEVENTF_EXTENDEDKEY, 0);
            keybd_event(VK_VOLUME_MUTE, MapVirtualKey(VK_VOLUME_MUTE, 0), KEYEVENTF_EXTENDEDKEY | KEYEVENTF_KEYUP, 0);
        }

        /// <summary>
        /// 当前音量值 0为静音状态
        /// </summary>
        public static int GetVolume()
        {
            UInt32 d, v;
            d = 0;
            long i = waveOutGetVolume(d, out v);
            UInt32 vleft = v & 0xFFFF;
            UInt32 vright = (v & 0xFFFF0000) >> 16;
            int currentVolume = (int.Parse(vleft.ToString()) | int.Parse(vright.ToString())) * 100 / 0xFFFF;
            return currentVolume;
        }

        /// <summary>
        /// 设置音量
        /// </summary>
        public static void SetVolume(int val)
        {
            UInt32 Value = (System.UInt32)((double)0xffff * (double)val / (double)100);//先把trackbar的value值映射到0x0000～0xFFFF范围
            //限制value的取值范围
            if (Value < 0) Value = 0;
            if (Value > 0xffff) Value = 0xffff;
            UInt32 left = (System.UInt32)Value;//左声道音量
            UInt32 right = (System.UInt32)Value;//右
            waveOutSetVolume(0, left << 16 | right); //"<<"左移，“|”逻辑或运算
        }
    }
}

```


# XML 配置
```c#
using System;
using System.IO;
using System.Xml;

namespace OnePhone
{
    class ConfigFile
    {

        protected readonly string configBasePath = "Root/Config";

        /// <summary>
        /// 当前配置文件的完整路径
        /// </summary>
        public string FileName { get; private set; }

        /// <summary>
        /// 获取或设置配置
        /// </summary>
        /// <param name="key">键名</param>
        /// <returns>键名对应的值</returns>
        public string this[string key]
        {
            get
            {
                return GetConfigValue(key);
            }
            set
            {
                AddOrSetConfigValue(key, value);
            }
        }

        /// <summary>
        /// 构造函数
        /// </summary>
        protected ConfigFile()
        {
        }

        /// <summary>
        /// 读取配置文件
        /// </summary>
        /// <param name="filename">配置文件名</param>
        /// <returns>ConfigFile对象</returns>
        public static ConfigFile LoadFile(string filename)
        {
            if (!File.Exists(filename)) return null;
            return new ConfigFile { FileName = filename };
        }

        /// <summary>
        /// 读取或创建配置文件
        /// </summary>
        /// <param name="filename">配置文件名</param>
        /// <returns>ConfigFile对象</returns>
        public static ConfigFile LoadOrCreateFile(string filename)
        {
            if (!File.Exists(filename))
            {
                var config = new ConfigFile { FileName = filename };
                config.CreateFile();
                return config;
            }
            return LoadFile(filename);
        }

        /// <summary>
        /// 创建配置文件，可在子类中重写此方法
        /// </summary>
        protected virtual void CreateFile()
        {
            XmlDocument doc = new XmlDocument();
            XmlDeclaration dec = doc.CreateXmlDeclaration("1.0", "UTF-8", null);
            doc.AppendChild(dec);
            XmlElement root = doc.CreateElement("Root");
            doc.AppendChild(root);
            root.AppendChild(doc.CreateElement("Config"));
            doc.Save(FileName);
        }

        /// <summary>
        /// 根据Key从配置文件中获取值
        /// </summary>
        /// <param name="key">键名</param>
        /// <returns>获取的值，找不到返回""</returns>
        public string GetConfigValue(string key)
        {
            return GetKeyValue($"{configBasePath}/{key}");
        }

        /// <summary>
        /// 从配置文件中根据key获取值并转化为相应数据类型
        /// </summary>
        /// <param name="key">键名</param>
        /// <param name="result">转化后的值</param>
        /// <returns>转化是否成功</returns>
        public bool TryParseConfigValue(string key, out int result)
        {
            var v = GetConfigValue(key);
            return int.TryParse(v, out result);
        }

        /// <summary>
        /// 从配置文件中根据key获取值并转化为相应数据类型
        /// </summary>
        /// <param name="key">键名</param>
        /// <param name="result">转化后的值</param>
        /// <returns>转化是否成功</returns>
        public bool TryParseConfigValue(string key, out bool result)
        {
            var v = GetConfigValue(key);
            return bool.TryParse(v, out result);
        }

        /// <summary>
        /// 从配置文件中根据key获取值并转化为相应数据类型
        /// </summary>
        /// <param name="key">键名</param>
        /// <param name="result">转化后的值</param>
        /// <returns>转化是否成功</returns>
        public bool TryParseConfigValue(string key, out DateTime result)
        {
            var v = GetConfigValue(key);
            return DateTime.TryParse(v, out result);
        }

        /// <summary>
        /// 从配置文件中根据key获取值并转化为相应数据类型
        /// </summary>
        /// <param name="key">键名</param>
        /// <param name="result">转化后的值</param>
        /// <returns>转化是否成功</returns>
        public bool TryParseConfigValue(string key, out float result)
        {
            var v = GetConfigValue(key);
            return float.TryParse(v, out result);
        }

        /// <summary>
        /// 从配置文件中根据key获取值并转化为相应数据类型
        /// </summary>
        /// <param name="key">键名</param>
        /// <param name="result">转化后的值</param>
        /// <returns>转化是否成功</returns>
        public bool TryParseConfigValue(string key, out double result)
        {
            var v = GetConfigValue(key);
            return double.TryParse(v, out result);
        }

        /// <summary>
        /// 从配置文件中根据key获取值并转化为相应数据类型
        /// </summary>
        /// <param name="key">键名</param>
        /// <param name="result">转化后的值</param>
        /// <returns>转化是否成功</returns>
        public bool TryParseConfigValue(string key, out decimal result)
        {
            var v = GetConfigValue(key);
            return decimal.TryParse(v, out result);
        }

        /// <summary>
        /// 在配置文件中根据key取出值并转化为int类型
        /// </summary>
        /// <param name="key"></param>
        /// <returns>返回的值</returns>
        public int GetConfigValueInt(string key)
        {
            var v = GetConfigValue(key);
            return int.Parse(v);
        }

        /// <summary>
        /// 在配置文件中根据key取出值并转化为bool类型
        /// </summary>
        /// <param name="key"></param>
        /// <returns>返回的值</returns>
        public bool GetConfigValueBool(string key)
        {
            var v = GetConfigValue(key);
            return bool.Parse(v);
        }

        /// <summary>
        /// 在配置文件中根据key取出值并转化为float类型
        /// </summary>
        /// <param name="key"></param>
        /// <returns>返回的值</returns>
        public float GetConfigValueFloat(string key)
        {
            var v = GetConfigValue(key);
            return float.Parse(v);
        }

        /// <summary>
        /// 在配置文件中根据key取出值并转化为double类型
        /// </summary>
        /// <param name="key"></param>
        /// <returns>返回的值</returns>
        public double GetConfigValueDouble(string key)
        {
            var v = GetConfigValue(key);
            return double.Parse(v);
        }

        /// <summary>
        /// 在配置文件中根据key取出值并转化为DateTime类型
        /// </summary>
        /// <param name="key"></param>
        /// <returns>返回的值</returns>
        public DateTime GetConfigValueDateTime(string key)
        {
            var v = GetConfigValue(key);
            return DateTime.Parse(v);
        }

        /// <summary>
        /// 在配置文件中根据key取出值并转化为decimal类型
        /// </summary>
        /// <param name="key"></param>
        /// <returns>返回的值</returns>
        public decimal GetConfigValueDecimal(string key)
        {
            var v = GetConfigValue(key);
            return decimal.Parse(v);
        }

        /// <summary>
        /// 修改Key对应的值，如果Key不存在则添加后存入值
        /// </summary>
        /// <param name="key">Key</param>
        /// <param name="value">要存入的值</param>
        public void AddOrSetConfigValue(string key, string value)
        {
            AddOrSetKeyValue(configBasePath, key, value);
        }

        /// <summary>
        /// 保存key/value键值对，对于非string类型会调用ToString()方法
        /// </summary>
        /// <typeparam name="T">要保存的值的类型</typeparam>
        /// <param name="key">要保存的键名</param>
        /// <param name="value">要保存的值</param>
        public void AddOrSetConfigValue<T>(string key, T value)
        {
            var v = value.ToString();
            AddOrSetConfigValue(key, v);
        }

        /// <summary>
        /// 保存key/value键值对
        /// </summary>
        /// <param name="basePath">要保存的根节点路径</param>
        /// <param name="key">要保存的键名</param>
        /// <param name="value">要保存的值</param>
        public void AddOrSetKeyValue(string basePath, string key, string value)
        {
            var doc = GetXmlDocument();
            var node = doc.SelectSingleNode($"{basePath}/{key}");
            if (node == null)
            {
                node = doc.CreateElement(key);
                doc.SelectSingleNode(basePath).AppendChild(node);
            }
            node.InnerText = value;
            doc.Save(FileName);
        }

        /// <summary>
        /// 根据xpath获取值
        /// </summary>
        /// <param name="xpath">要获取的路径</param>
        /// <returns>获取的值</returns>
        public string GetKeyValue(string xpath)
        {
            var doc = GetXmlDocument();
            var node = doc.SelectSingleNode(xpath);
            return node?.InnerText ?? "";
        }

        /// <summary>
        /// 删除Key以及对应的值
        /// </summary>
        /// <param name="key">Key</param>
        public void DeleteConfigKey(string key)
        {
            var doc = GetXmlDocument();
            var node = doc.SelectSingleNode($"{configBasePath}/{key}");
            if (node == null) return;
            doc.SelectSingleNode("Root/Config")?.RemoveChild(node);
            doc.Save(FileName);
        }

        /// <summary>
        /// 读取XML文档
        /// </summary>
        /// <returns></returns>
        private XmlDocument GetXmlDocument()
        {
            var doc = new XmlDocument();
            doc.Load(FileName);
            return doc;
        }

    }
}

```


# 签名类
```c#
using System;
using System.IO;
using System.Security.Cryptography;
using System.Text;


namespace OnePhone
{
    //密码加密解密方法
    class MyEncrypt
    {

        private static byte[] Keys = { 0x12, 0x34, 0x56, 0x78, 0x90, 0xAB, 0xCD, 0xEF };

        private static string encryptKey = "liushuixingyun";

        /// <summary>
        /// DES加密字符串
        /// </summary>
        /// <param name="encryptString">待加密的字符串</param>
        /// <param name="encryptKey">加密密钥,要求为8位</param>
        /// <returns>加密成功返回加密后的字符串，失败返回源串</returns>
        public static string EncryptDES(string encryptString)
        {
            try
            {
                byte[] rgbKey = Encoding.UTF8.GetBytes(encryptKey.Substring(0, 8));
                byte[] rgbIV = Keys;
                byte[] inputByteArray = Encoding.UTF8.GetBytes(encryptString);
                DESCryptoServiceProvider dCSP = new DESCryptoServiceProvider();
                MemoryStream mStream = new MemoryStream();
                CryptoStream cStream = new CryptoStream(mStream, dCSP.CreateEncryptor(rgbKey, rgbIV), CryptoStreamMode.Write);
                cStream.Write(inputByteArray, 0, inputByteArray.Length);
                cStream.FlushFinalBlock();
                return Convert.ToBase64String(mStream.ToArray());
            }
            catch
            {
                return encryptString;
            }
        }

        /// <summary>
        /// DES解密字符串
        /// </summary>
        /// <param name="decryptString">待解密的字符串</param>
        /// <param name="decryptKey">解密密钥,要求为8位,和加密密钥相同</param>
        /// <returns>解密成功返回解密后的字符串，失败返源串</returns>
        public static string DecryptDES(string decryptString)
        {
            try
            {
                byte[] rgbKey = Encoding.UTF8.GetBytes(encryptKey);
                byte[] rgbIV = Keys;
                byte[] inputByteArray = Convert.FromBase64String(decryptString);
                DESCryptoServiceProvider DCSP = new DESCryptoServiceProvider();
                MemoryStream mStream = new MemoryStream();
                CryptoStream cStream = new CryptoStream(mStream, DCSP.CreateDecryptor(rgbKey, rgbIV), CryptoStreamMode.Write);
                cStream.Write(inputByteArray, 0, inputByteArray.Length);
                cStream.FlushFinalBlock();
                return Encoding.UTF8.GetString(mStream.ToArray());
            }
            catch
            {
                return decryptString;
            }
        }

    }
}

```

#检查网络是否异常
```c#
using System;
using System.Collections.Generic;
using System.Text;
//方法一
using System.Runtime;
using System.Runtime.InteropServices;
//方法二 Net2.0新增类库
using System.Net.NetworkInformation;

namespace OnePhone
{
    public class Internet
    {
       /// <summary>
       /// 方法三
       /// </summary>
       /// <param name="ip"></param>
       /// <returns></returns>
        public static bool DoPing(string ip)
        {
            Ping pingSender = new Ping();
            PingOptions options = new PingOptions();
            options.DontFragment = true;
            string data = "a";
            byte[] buffer = Encoding.ASCII.GetBytes(data);
            int timeout = 120;

            PingReply reply = pingSender.Send(ip, timeout, buffer, options);
            return reply.Status == IPStatus.Success;

        }

        [DllImport("wininet.dll")]
        private extern static bool InternetGetConnectedState(int Description, int ReservedValue);

        #region 方法一
        /// <summary>
        /// 用于检查网络是否可以连接互联网,true表示连接成功,false表示连接失败
        /// </summary>
        /// <returns></returns>
        public static bool IsConnectInternet()
        {
            int Description = 0;
            return InternetGetConnectedState(Description, 0);
        }
        #endregion

        #region 方法二
        /// <summary>
        /// 用于检查IP地址或域名是否可以使用TCP/IP协议访问(使用Ping命令),true表示Ping成功,false表示Ping失败
        /// </summary>
        /// <param name="strIpOrDName">输入参数,表示IP地址或域名</param>
        /// <returns></returns>
        public static bool PingIpOrDomainName(string strIpOrDName)
        {
            try
            {
                Ping objPingSender = new Ping();
                PingOptions objPinOptions = new PingOptions();
                objPinOptions.DontFragment = true;
                string data = "";
                byte[] buffer = Encoding.UTF8.GetBytes(data);
                int intTimeout = 120;
                PingReply objPinReply = objPingSender.Send(strIpOrDName, intTimeout, buffer, objPinOptions);
                string strInfo = objPinReply.Status.ToString();
                if (strInfo == "Success")
                {
                    return true;
                }
                else
                {
                    return false;
                }
            }
            catch (Exception)
            {
                return false;
            }
        }
        #endregion
    }
}

```
