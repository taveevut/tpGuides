# ขั้นตอนการติดตั้ง Oci8 สำหรับเชื่อมฐานข้อมูล Oracle สำหรับระบบ OSX

### สิ่งที่ต้องติดตั้งก่อนลง PHP OCI8 install
  -  Xcode Command Line Tools
  -  PEAR และ PECL
  -  BREW
### ดาวน์โหลด `SQL Oracle 3 Packages` เพื่อเตรียมนำมาติดตั้งจาก [Download Oracle Instant Client](https://www.oracle.com/database/technologies/instant-client/macos-intel-x86-downloads.html)
>  หมายเหตุ ตรวจดูระบบปฏิบัติการ 32 bit หรือ 64 bit ก่อนดาวน์โหลดไฟล์ ส่วนใหญ่เครื่อง Mac หลังปี 2007 จะเป็น 64 บิต ดาวน์โหลดดังนี้
   - `Instant Client Package - Basic`: All files required to run OCI, OCCI, and JDBC-OCI applications [Attach/Oci8/instantclient-basic-macos.x64-18.1.0.0.0.zip](/instantclient-basic-macos.x64-18.1.0.0.0.zip)
   - `Instant Client Package - SQL*Plus`: Additional libraries and executable for running SQL*Plus with Instant Client [Attach/Oci8/instantclient-sqlplus-macos.x64-18.1.0.0.0.zip](/instantclient-sqlplus-macos.x64-18.1.0.0.0.zip)
   - `Instant Client Package - SDK`: Additional header files and an example makefile for developing Oracle applications with Instant Client [Attach/Oci8/instantclient-sdk-macos.x64-18.1.0.0.0-2.zip](/instantclient-sdk-macos.x64-18.1.0.0.0-2.zip)
#### สร้างโฟลเดอร์ /opt/oracle/instantclient_18_1/ สำหรับ unzip ไฟล์ที่ดาวน์โหลดมา โดยใช้คำสั่งผ่าน Terminal หรือสร้างโฟลเดอร์ขึ้นเองก็ได้ 
- คำสั่งสร้างโฟลเดอร์
```
sudo mkdir /opt
sudo mkdir /opt/oracle
```
- สำหรับ unzip ไฟล์ไปยังโฟลเดอร์ โดยใช้ Terminal ให้พิมพ์คำสั่งข้างล่าง ใน Directory ที่เก็บไฟล์ที่ดาวน์โหลดมา ตัวอย่างการเข้ายัง Directory ที่เก็บไฟล์ที่ดาวน์โหลดมา `cd ~/Downloads/`
```
sudo unzip -d /opt/oracle/ instantclient-basic-macos.x64-18.1.0.0.0.zip
sudo unzip -d /opt/oracle/ instantclient-sdk-macos.x64-18.1.0.0.0-2.zip
sudo unzip -d /opt/oracle/ instantclient-sqlplus-macos.x64-18.1.0.0.0.zip
```

- แก้ไขไฟล์ `.zshrc`  ที่ Home Directory คำสั่ง nano สำหรับเปิดไฟล์เพื่อแก้ไข
```
sudo nano ~/.zshrc
```
เพิ่มโค้ดในไฟล์ `.zshrc` ตามนี้
```
export ORACLE_HOME=/opt/oracle/instantclient_18_1
export DYLD_LIBRARY_PATH=/opt/oracle/instantclient_18_1
export PATH=$PATH:$DYLD_LIBRARY_PATH
export SQL_PATH=$ORACLE_HOME
export PATH=$PATH:$ORACLE_HOME
```
กด ctrl + x เพื่อออกจากการแก้ไขไฟล์ ตอบ yes เพื่อทำการบันทึก
เมื่อบันทึกสำเร็จให้พิมพ์คำสั่ง
```
source ~/.zshrc
```

ทำการเชื่อมโยง Lib ไปยัง directory `/opt/oracle/instantclient_18_1/`
```
cd /opt/oracle/instantclient_18_1/
```

เพิ่มคำสั่ง
```
ln -sf libclntsh.dylib.18.1 libclntsh.dylib
ln -sf libocci.dylib.18.1 libocci.dylib
```


### เริ่มทำการติดตั้ง OCI8
> ตรวจสอบเวอร์ชันของ PEAR และ PHP ก่อนโดยใช้คำสั่ง `pecl version `
```
  PEAR Version: 1.10.14
  PHP Version: 7.4.33
  Zend Engine Version: 3.4.0 
```
  
- ติดตั้ง autoconf
``` 
  brew install autoconf
```

- ถ้าเคยติดตั้ง OCI8 แล้ว ให้ Uninstall ก่อน แล้วค่อยติดตั้งใหม่อีกครั้ง
  > ถ้าเป็นเวอร์ชันล่าสุด `sudo pecl install oci8` แต่สำหรับ PHP ^7.4 จะเป็น `sudo pecl uninstall oci8-2.2.0` หากต้องการข้อมูลเพิ่มเติมสามารถสอบถาม Chat-GPT ได้
```
sudo pecl uninstall oci8-2.2.0 
```

- Install OCI8
```
sudo pecl install oci8-2.2.0 
```

- หรือ (-f คือการ rebuild)
```
sudo pecl install -f oci8-2.2.0 
```

> PECL จะถามที่อยู่ของ `ORACLE_HOME`
`Please provide the path to the ORACLE_HOME directory. Use 'instantclient,/path/to/instant/client/lib' if you're compiling with Oracle Instant Client [autodetect] :`

> ให้พิมพ์ directory ที่ unzip Oracle Instant Client
`instantclient,/opt/oracle/instantclient_18_1`

> ถ้าติดตั้งเสร็จสมบูรณ์จะแสดง
`
Build process completed successfully
Installing '/usr/lib/php/extensions/no-debug-non-zts-XXXXxxXX/oci8.so'
install ok: channel://pecl.php.net/oci8-X.X.X
configuration option "php_ini" is not set to php.ini location
You should add "extension=oci8.so" to php.ini
`
- เปิด Extension ใน php.ini
  > ในเครื่อง MacOSX สามารถใช้คำสั่งเพื่อที่อยู่ของไฟล์ php.ini ผ่าน Terminal ได้ โดยใช้คำสั่ง `php --ini`
```
extension=oci8.so
```