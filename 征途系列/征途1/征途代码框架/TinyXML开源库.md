[TOC]
# TinyXML开源库
概括：TinyXML是一个开源的解析Xml文件的C++库，Windows和Linux环境下都能编译。主要相关文件应该是这几个:tinyxml.h、tinystr.h、tinystr.cpp、tinyxml.cpp、tinyxmlerror.cpp、tinyxmlparser.cpp

## 使用流程
> 如下是一个XML片段：
```xml
<Persons>
        <Person ID="1">
            <name>周星星</name>
            <age>20</age>
        </Person>
        <Person ID="2">
            <name>白晶晶</name>
            <age>18</age>
        </Person>
</Persons>
```
在TinyXML中，根据XML的各种元素来定义了一些类：
- TiXmlBase：整个TinyXML模型的基类。
- TiXmlAttribute：对应于XML中的元素的属性。
- TiXmlNode：对应于DOM结构中的节点。
- TiXmlComment：对应于XML中的注释。
- TiXmlDeclaration：对应于XML中的申明部分，即<？versiong="1.0" ?>。
- TiXmlDocument：对应于XML的整个文档。
- TiXmlElement：对应于XML的元素。
- TiXmlText：对应于XML的文字部分。
- TiXmlUnknown：对应于XML的未知部分。 
- TiXmlHandler：定义了针对XML的一些操作。

> 读取XML(假设我们的Xml文档中的内容与上面的Xml内容一样)

```C++
//创建一个XML的文档对象。
    TiXmlDocument *myDocument = new TiXmlDocument("Xml文件名");
    myDocument->LoadFile();
    //获得根元素，即Persons。
    TiXmlElement *RootElement = myDocument.RootElement();
    //输出根元素名称，即输出Persons。
    cout << RootElement->Value() << endl;
    //获得第一个Person节点。
    TiXmlElement *FirstPerson = RootElement->FirstChildElement();
    //获得第一个Person的name节点和age节点和ID属性。
    TiXmlElement *NameElement = FirstPerson->FirstChildElement();
    TiXmlElement *AgeElement = NameElement->NextSiblingElement();
    TiXmlAttribute *IDAttribute = FirstPerson->FirstAttribute();
    //输出第一个Person的name内容，即周星星；age内容，即20；ID属性，即1。
    cout << NameElement->FirstChild()->Value << endl;
    cout << AgeElement->FirstChild()->Value << endl;
    cout << IDAttribute->Value() << endl;
```

## API使用方法
### 创建文档对象
创建一个空的文档对象，然后载入一个xml文档
使用到的函数原形如下：
```C++
TiXmlDocument();
bool LoadFile( const std::string& filename)
```
在程序中你可以如下使用：
```C++
//载入xml文档
TiXmlDocument doc();
doc.LoadFile("tutorial.xml");
```
在构造函数中传入文档的名称，然后调用load函数完成解析载入
使用到的函数原形如下：
```C++
TiXmlDocument( const std::string& documentName );
bool LoadFile();
```
在程序中你可以如下使用：
```C++
//载入xml文档
TiXmlDocument doc("tutorial.xml");
doc.LoadFile(); 

```
### 输出文档对象
文档类提供了Print()函数用于在控制台输出当前的文档内容，这个函数的原形如下：
```C++
void Print() const
```
在程序中你可以如下使用：
```C++
//载入xml文档
TiXmlDocument doc("tutorial.xml");
doc.LoadFile();
doc.Print(); //输出文档 
```

tutorial.xml的内容如下：
```xml
<?xml version="1.0" standalone="yes" encoding="utf-8"?>

<!--comment 注释-->

<element attribute="this a attribute(这是一个属性)" int= "1" float = "3.14">
<subelement1>
   This a text(这是一个文本)
</subelement1>
<subelement2/>
<subelement3/>
<subelement4/> 
</element> 
```

### 保存文档对象
当然你也可以使用SaveFile()函数来进行另存为，这个函数的原形如下：
```C++
bool SaveFile( const std::string& filename ) const
```
在程序中你可以如下使用：
```C++
//载入xml文档
TiXmlDocument doc("tutorial.xml");
doc.LoadFile();
doc.Print(); //输出文档
cout<<endl;
doc.SaveFile("tutorial.txt"); 
```
### 返回第一个根元素
另外文档对象还提供了一个实用的函数用于返回第一个根对象，它可以让你方便的遍历整个文档结构，查找自己需要的数据。函数原形如下：
```C++
TiXmlElement* RootElement()
```
我们在介绍元素类的时候再详细介绍它的使用。

### 声明类
在标准的XML文件中，声明为文件的第一项，例如<?xml version="1.0" standalone="yes"?>,声明对象具有三个属性值，版本，编码和独立文件声明
一般来说文档的第一行就是声明对象，你可以把文档对象的第一个子节点转换为声明对象。
```C++
//使用TinyXml的声明对象
TiXmlDeclaration *decl;
decl = doc.FirstChild()->ToDeclaration(); 
```
然后就可以使用它的功能了，它可以让你返回当前的版本，编码等信息，函数原形如下：
```C++
const char *Version() const
const char *Encoding() const
const char *Standalone() const
```

在程序中你可以如下使用：
```C++
//使用TinyXml的声明对象
TiXmlDeclaration *decl;
decl = doc.FirstChild()->ToDeclaration();
cout<<"使用TinyXml的声明对象(TiXmlDeclaration)"<<endl;
//输出声明对象对应的xml内容
decl->Print(0,4,&str);
cout<<str<<endl;
//分别输出声明对象的属性
cout<<"版本："<<decl->Version()<<" 是否为对立文件："<<decl->Standalone()<<" 编码方式："<<decl->Encoding()<<endl;
cout<<endl;  
```

### 元素类
元素为一个容器类，它具有元素名称，并可以包含其它元素，文本，注释和未知节点，这些对象统称为元素的节点，即节点可以为元素、文本、注释和未知节点类型。元素也可以包含任意个数的属性。
我们还是以如下的XML代码来说明这个类的功能。
```xml
<element attribute="this a attribute(这是一个属性)" int= "1" float = "3.14">
<subelement1>
   This a text(这是一个文本)
</subelement1>
<subelement2/>
<subelement3/>
<subelement4/> 
</element>
```

#### 节点名
在上方元素的代码中，element为根元素的名称，你可以通过如下的函数来设置和返回它。
```C++
const std::string& ValueStr() const
void SetValue( const std::string& _value )
```

#### 父节点
subelement1，subelement2，subelement3，subelement4都是element的子元素，如果当前元素对象的指针指向subelement1，subelement2，subelement3，subelement4，你可以通过Parent()函数来返回指向element对象的指针，Parent()函数的声明如下：
```C++
TiXmlNode* Parent()
```

#### 子节点
通过父节点的指针，你可以遍历所有的子节点。
```C++
TiXmlNode* FirstChild()
TiXmlNode* FirstChild( const std::string& _value )
```
上面两个函数用于返回第一个子节点对象的指针，带参数名的那个函数表示返回第一个名为_value的子节点。

```C++
TiXmlNode* LastChild()
TiXmlNode* LastChild( const std::string& _value )
```
上面的两个函数用于返回最后一个节点对象的指针，带参数名的那个函数表示返回最后一个名为_value的子节点。

你也可以使用IterateChildren()函数来依次遍历所有的节点，它们的函数声明如下：
```C++
TiXmlNode* IterateChildren( const TiXmlNode* previous )
TiXmlNode* IterateChildren( const std::string& _value, const TiXmlNode* previous )
```
带参数名的那个函数表示只遍历同名的节点。

#### 编辑子节点
你可以插入、删除替换所有的子节点。
```C++
TiXmlNode* InsertEndChild( const TiXmlNode& addThis );
TiXmlNode* InsertBeforeChild( TiXmlNode* beforeThis, const TiXmlNode& addThis );
TiXmlNode* InsertAfterChild( TiXmlNode* afterThis, const TiXmlNode& addThis );
```
上面三个函数用于插入节点，InsertEndChild函数让你把新节点插入到末尾，InsertBeforeChild和InsertAfterChild函数允许你在指定的节点位置前后插入节点。
```C++
TiXmlNode* ReplaceChild( TiXmlNode* replaceThis, const TiXmlNode& withThis );
```

ReplaceChild函数用于替换指定的节点。
```C++
bool RemoveChild( TiXmlNode* removeThis );
```
RemoveChild函数让你删除指定的节点。
```C++
void Clear();
```
Clear函数会删除本节点的所有子节点（包括子节点包含的从子节点），但不会修改本节点。

#### 同级节点
```xml
<element attribute="this a attribute(这是一个属性)" int= "1" float = "3.14">
<subelement1>
   This a text(这是一个文本)
</subelement1>
<subelement2/>
<subelement3/>
<subelement4/> 
</element> 
```
在上面的xml代码中，subelement1、subelement2、subelement3、subelement4都属于同级节点，我们也提供了相关的函数用于在这些同级节点中遍历。
```C++
TiXmlNode* PreviousSibling()
TiXmlNode* PreviousSibling( const std::string& _value )
```
可以根据当前的节点，返回上一个节点的指针。带参数名的那个函数表示返回上一个名为_value的节点。

当然你也可以根据当前的节点，返回下一个节点的指针。带参数名的那个函数表示返回下一个名为_value的节点。
```C++
TiXmlNode* NextSibling()
TiXmlNode* NextSibling( const std::string& _value)
```

#### 遍历元素
元素是一种特殊的节点，以’<’为开始字符，后接元素名称。函数NextSiblingElement用于返回下一个同级元素，而忽略其它类型的节点。它们的函数声明如下：
```C++
TiXmlElement* NextSiblingElement()
TiXmlElement* NextSiblingElement( const std::string& _value)
```
带参数名的那个函数表示返回下一个名为_value的同级元素。

本类也提供了相关的函数，让你返回第一个子元素。
```C++
TiXmlElement* FirstChildElement()
TiXmlElement* FirstChildElement( const std::string& _value )
```
带参数名的那个函数表示返回下一个名为_value的子元素。

#### 元素属性
属性一般保存在元素中，它们为使用“=”号连接的两个字符串，左边的表示属性名，等号右边的表示属性值，通常使用字符串、整数和浮点数等数据类型表示。例如，pi = 3.14。
你可以通过如下的函数，返回属性值。
```C++
const std::string* Attribute( const std::string& name ) const;
const std::string* Attribute( const std::string& name, int* i ) const;
const std::string* Attribute( const std::string& name, double* d ) const;
```
在上面3个函数中，第一个函数使用字符串保存返回的属性值，第二个函数把属性值转换为整数然后返回，第三个函数把属性值转换为浮点数然后返回。不过，第二、三个函数都会以字符串的形式记录属性值，并作为函数的返回值返回。
另外，你也可以使用模板函数：
```C++
template< typename T > int QueryValueAttribute( const std::string& name, T* outValue ) const
```
来返回特点的属性值，它会根据你传入的参数，自动选择合适数据类型。

另外，本类也提供了如下三个函数让你设置属性，参数的类型和返回函数类似。
```C++
void SetAttribute( const std::string& name, const std::string& _value );
void SetAttribute( const std::string& name, int _value );
void SetDoubleAttribute( const char * name, double value );
```

FirstAttribute和LastAttribute可以让你返回第一个和最后一个属性，它们的函数声明如下：
```C++
TiXmlAttribute* FirstAttribute()
TiXmlAttribute* LastAttribute() 
```
RemoveAttribute函数可以让你删除指定名称的属性，它的函数声明如下：
```C++
void RemoveAttribute( const std::string& name )
```

####**元素函数总结**
ValueStr     //返回元素名称
SetValue     //设置元素名称
Parent     //返回父节点对象

FirstChild    //返回第一个子节点
LastChild     //返回最后一个子节点
IterateChildren   //返回下一个子节点

InsertEndChild   //在最后一个子节点后插入子节点
InsertBeforeChild   //在指定的子节点前插入子节点
InsertAfterChild   //在指定的子节点后插入子节点
ReplaceChild    //替换指定的子节点
RemoveChild    //删除指定的子节点
Clear     //删除所有的子节点

PreviousSibling   //返回同级中前一个节点
NextSibling    //返回同级中后一个节点

NextSiblingElement   //返回同级中后一个元素
FirstChildElement   //返回第一个子元素节点
Attribute     //返回元素中的属性值
QueryValueAttribute //返回元素中的属性值
SetAttribute    //设置元素中的属性值
FirstAttribute   //返回元素中第一个属性对象
LastAttribute    //返回元素中最后一个属性对象
RemoveAttribute   //删除元素中指定的属性对象

###属性类
属性为名称="值"对，元素可以具有属性值，但名称必须唯一。
你可以通过
```C++
const std::string& NameTStr() const
```
返回属性名称

也可以通过下面三个函数返回属性值：
```C++
const std::string& ValueStr() const
int     IntValue() const;
double    DoubleValue() const;
```
当然你也可以设置属性值，它们的函数声明如下：
```C++
void SetName( const std::string& _name )
void SetIntValue( int _value ); 
void SetDoubleValue( double _value );
void SetValue( const std::string& _value )
```
以上函数与元素类中的相关函数类似，这里不重复介绍了。

在元素属性中，通常具有许多属性，你可以通过Next函数返回下一个属性对象的指针，也可以通过Previous函数获得上一个属性对象的指针。它们的函数声明如下：
```C++
TiXmlAttribute* Next()
TiXmlAttribute* Previous()
```