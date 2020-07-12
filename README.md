# TT文本模板代码生成器

### (对于重复性且依赖于一项文件需要对应生成代码)

```C#
//当我们的有需要生成固定且依赖一项 如：
int[] iarr = new int[7]{1,2,3,34,2,1,123};
//我们需要对数组每项生成对应的代码
public class Temp1
{}
public class Temp2
{}
//我们可以使用文本模板生成
```



### 一、使用方式

#### 1、创建一个tt文本模板

```C#
<#@ template language="C#" debug="false" hostspecific="true"#>
<#@ output extension=".cs"#>
<#int[] iarr = new int[7]{1,2,3,34,2,1,123};#>
<#foreach(int item in iarr){#>
public class Temp<#=item#>
{}
<#}#>
```

保存即可生成对应的代码

### 二、使用EF生成数据实体类（当数据库发生改变，我们需要生成对应的代码，所以我们使用TT文本模板能快速的生成基本代码）

#### 1、使用TT文本生成器生成IDBSession

```C#
<#@ template language="C#" debug="false" hostspecific="true"#>
<#@ include file="EF.Utility.CS.ttinclude"#>
<#@ output extension=".cs"#> 
<#
CodeGenerationTools code = new CodeGenerationTools(this);
MetadataLoader loader = new MetadataLoader(this);
CodeRegion region = new CodeRegion(this, 1);
MetadataTools ef = new MetadataTools(this);
string inputFile = @"..\\TestOA.Model\\TestOA.edmx";//EF实体模型位置
EdmItemCollection ItemCollection = loader.CreateEdmItemCollection(inputFile);
string namespaceName = code.VsNamespaceSuggestion();
EntityFrameworkTemplateFileManager fileManager = EntityFrameworkTemplateFileManager.Create(this);#>
//命名空间，我们需要生成对应的实体接口，当项目不一样是我们需要改变命名空间
using System.Collections.Generic;
using System.Data.Entity;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using TestOA.DAL;
using TestOA.IDAL;
using TestOA.Model;

namespace TestOA.DALFactory
{
	public partial class DBSession : IDBSession
    {
    //循环生成
	<#foreach (EntityType entity in ItemCollection.GetItems<EntityType>().OrderBy(e => e.Name)){#>
		private I<#=entity.Name#>Dal _<#=entity.Name#>Dal;

        public I<#=entity.Name#>Dal <#=entity.Name#>Dal
        {
            get
            {
                if (_<#=entity.Name#>Dal == null)
                {
                    _<#=entity.Name#>Dal = AbstractFactory.Create<#=entity.Name#>Dal();//通过抽象工厂封装了类的实例的创建
                }
                return _<#=entity.Name#>Dal;
            }

            set
            {
                _<#=entity.Name#>Dal = value;
            }
        }
	<#}#>
	}
}
```

#### 2、相应的文件存在根目录TT文本模板

![image-20200712155047594](image-20200712155047594.png)