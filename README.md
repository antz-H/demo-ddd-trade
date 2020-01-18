这是一个整洁架构技术落地的示例。然而，该示例演示的架构，比整洁架构更加简洁、更易于维护、更易于进行技术架构的更迭。有了这样强有力的架构支持，就可以让开发团队有更快的交付速度，更好地应对市场地快速更迭。

整洁架构（The Clean Architecture）是Robot C. Martin在《架构整洁之道》中提出来的架构设计思想。整洁架构设计以圆环的形式把系统分成了几个不同的部分，其中心是业务实体（Entity）与业务应用（Application），业务实体就是领域模型中的实体与值对象，业务应用就是面向用户的那些服务（Service）。它们合起来组成了业务领域层，也就是通过领域模型的分析，然后运用充血模型或者贫血模型，从而形成的业务代码的实现。整洁架构的最外层是各种技术框架，包括与用户UI的交互、客户与服务器的网络交互、与硬件设备与数据库的交互，以及与其它外部系统的交互。而整洁架构的精华在中间的适配器层，它通过适配器将核心的业务代码与外围的技术框架进行解耦。因此，如何设计这个适配层，让业务代码与技术框架解耦，让业务开发团队与技术架构团队各自独立地工作，成为了整洁架构落地的核心。

为了实现整洁架构，通常采用Controller将用户UI界面与业务领域层解耦，用数据访问层DAO与数据库解耦，来设计适配器层，从而将业务领域层与外围的技术框架解耦。然而，在设计实现时，通常要为每一个业务功能单独实现一个Controller，再单独实现一个DAO，使得整个系统要编写大量的Controller与DAO，使得系统代码太多，过于复杂，开发工作量大的同时，日后也难于维护，变更成本太高。

为了解决以上问题，让代码更加整洁，本示例在增删改的业务操作时，采用了单Controller与单DAO的形式，即整个系统就只有一个Controller与一个DAO。

单Controller的设计：所有功能的UI在访问后台的时候都只访问一个Controller{@see com.demo2.support.web.OrmController}，但传入的是不同的bean和method，通过spring就可以查找到相应的service，然后通过反射就可以查找到相应的方法。为了让前端的提交的Json能通过一个通用程序自动转换成值对象，该框架要求开发人员将前端的Json与后台的值对象对应起来，长得一模一样。这样，通过反射就自动化地将Json转换为值对象，然后再通过去调用后台的service。

单DAO的设计：当各个Service在完成了各自的业务操作以后，就要去数据持久化的操作。在本示例中，通过vObj.xml文件，将各个值对象与数据库中的表对应起来。这样，所有的Service都注入的是那一个DAO。当这一个DAO要做持久化时，它拿到的是哪个类型的值对象，就知道持久化到数据库的哪个表中。

采用以上的设计，不仅简化了代码的编写，也规范了代码的编写，即业务开发人员没有机会将业务代码写到Controller与DAO中，必然就会按照充血模型与贫血模型，将业务代码写到Service与值对象中。通过该设计，也使得业务代码与技术框架解耦，从而能够更好地应对技术框架的更迭，进行架构演化。

本示例可以从客户端访问的服务：

service-customer:

curl -X POST http://localhost:8800/query/customerQry

curl -X POST http://localhost:8800/orm/customer/save -d "id=0005&name=Youngman&sex=male&birthday=2013-07-08&identification=110212201307083814&phoneNumber=13477496662"

curl http://localhost:8800/orm/customer/delete?id=0005

curl http://localhost:8800/orm/customer/listOfCustomers

service-product:

curl -X POST http://localhost:8800/query/productQry

curl -X POST http://localhost:8800/orm/product/saveProduct -d "id=P00006&name=ThinkPad+T220&price=4600&unit=%E4%B8%AA&supplierId=S0002&classify=%E5%8A%9E%E5%85%AC%E7%94%A8%E5%93%81"

curl http://localhost:8800/orm/product/deleteProduct?id=P00006

service-supplier:

curl http://localhost:8800/orm/supplier/loadSupplier?id=S0002
