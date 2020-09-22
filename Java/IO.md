# IO流整体结构

<img src="笔记图片/IO/image-20200917170045822.png" alt="image-20200917170045822" style="zoom: 150%;" />



Java  IO，按操作对象分类结构图：

![image-20200917170204157](笔记图片/IO/image-20200917170204157.png)



# File类

**File类是对文件系统中及文件以及文件夹进行操作的类，可以通过面向对象的思想来操作文件和文件夹。**

**1.使用File类对文件进行操作**

```java
@Test
public void fileTest() throws IOException {

    String fileName = "H:\\routes.txt";
    File file = new File(fileName);   // 初始化File对象
    if (file.exists()) {              // exists()方法判断这个文件是否存在
        System.out.println("文件已存在。");
        // file.delete();             // 删除文件
    }else {
        file.createNewFile();        // createNewFile()方法在File中指定的路径新建文件
        System.out.println("新建文件:" + fileName);
    }

    System.out.println(File.separator); // windows下的分隔符为\
    System.out.println(File.pathSeparator); // windows下的分隔符为;
}
```



**2.使用File类也可以操作文件夹**

```java
@Test
public void directoryTest() throws IOException {
    String directoryName = "H:\\48h_frequency_data";
    File directory = new File(directoryName);

    if (directory.isDirectory()) { // 判断是否是文件夹
        String[] fileNames = directory.list(); // 列出文件夹下所有的文件
        File[] files = directory.listFiles();  // 列出所有文件，包括隐藏文件
        for (String fileName : fileNames) {
            System.out.println(fileName);
        }
        
        System.out.println("===================================");
        
        for (File file : files) {
            System.out.println(file.getName());
        }
    }
	
    System.out.println("===============================");
    // 在指定文件夹下新建文件，前提：文件夹存在
    File file = new File("H:\\48h_frequency_data", "new.txt");
    file.createNewFile();
    if (file.exists()) {
        System.out.println("文件名：" + file.getName());
    }
    
    //第三种创建文件夹的构造函数
    File parent = new File("H:\\")
	File directory = new File(parent, "48_frequency_data");
}
```



**三种创建文件夹File构造函数：上面的例子使用了第一种和第二种方法**

```java
File(String directoryPath);
File(String directoryPath, String filename);
File(File dirObj, String filename);
```





# 从控制台读取输入：

```java
BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(System.in));
String line;
while ((line = bufferedReader.readLine()) != null) {
    System.out.println(line);
}
```



# 从文件读取输入

**1.使用FileInputStream读取字节，并将字节转换成字符串：**

**2.使用BufferedReader读取字符，使用BufferedReader可以按行读入字符**

```java
@Test
public void fileInputStreamTest() throws IOException {
    File file = new File("H:\\routes.txt");          // 新建File对象
    FileInputStream in = new FileInputStream(file);  // 新建FileInputStream对象，传入File
    int available = in.available();                  // available()方法返回文件的字节总数
    byte[] bytes = new byte[available];              // 创建一个byte数组，存储所有的字节
    in.read(bytes);                                  // 读取字节，并存入字节数组中
    System.out.println(new String(bytes));           // 将byte数组转换成String
                                                  //使用编译平台默认的字符集（Unicode）
    System.out.println("=======================");

    BufferedReader bufferedReader = new BufferedReader(new FileReader(file)， 20);
    // 根据官方API，BufferedReader构造函数需要一个Reader，
    // 这里需要读文件，所以需要传入一个FileReader，第二个参数用来自定义Buffer的大小
    System.out.println(bufferedReader.readLine());
}
}
```

