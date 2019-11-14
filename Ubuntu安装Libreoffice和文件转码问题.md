### Ubuntu安装libreoffice，用于Word转pdf，并解决中文乱码问题

####安装libreoffice:

[下载安装包](https://drive.google.com/file/d/1ENnMV2_cj4s6H71C4F2ELw_nVl_EFBKw/view?usp=sharing)
- 安装
```bash
  sudo dpkg -i ./LibreOffice_6.2.8.2_Linux_x86-64_deb/DEBS/*.deb    /* 安装主安装程序的所有deb包 */
  sudo dpkg -i ./LibreOffice_6.2.8.2_Linux_x86-64_deb_langpack_zh-CN/DEBS/*.deb  /* 安装中文语言包中的所有deb包 */
```
- 安装过后在启动转化的时候会报缺少各种依赖（安装下面的包可以安装所有能用到的依赖）
```bash
 sudo apt-get install libreoffice-pdfimport
```
- 然后运行转化脚本（将word文件转化为pdf）
```bash
libreoffice6.2 --convert-to pdf:writer_pdf_Export 1571131529.docx --outdir /home/xaohuihui
```
#### 转化中文乱码的问题
- 在上一步运行之前是不会在 .config 下创建 .libreoffice文件夹的，产生的文件夹刚好可以进行解决中文乱码问题

- 最完美的方案：解决转码中文乱码问题： http://www.yanglajiao.com/article/qq_30554229/80093894
 Linux是多用户的，但是我们自己的电脑通常只用一个普通用户，so，我们只需让字体对自己生效就行了，这样不会破坏系统字体设置。
打开主文件夹
按Ctrl+H显示隐藏文件夹，打开.libreoffice （也有的在.confing/libreoffice,比如ubuntu 12.04）
依次进入到4/user,新建文件夹fonts
然后把将下载安装包中的Fonts中的字体复制到fonts这个文件夹下即可.

还有一点,libreoffice不支持并发,所以当两个文件同时转换的时候会出问题,因此在对文件转换时需要加锁.我现在是对某个文件进行加锁.处理完成释放锁
