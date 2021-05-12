---
Title: "Powershell"
Weight: 8
Description: >
  Powershell commands
---

## How to use Powershell commands

There are some commands to create and handle endpoints much easier than before.   
When these commands are executed, they will automatically add templates(c#) files to your project.
You can add your business logic within these template files.
In this way the functionality is added to your dll after building your project.


## Available commands
- Add-PCTEndpoint - creates an endpoint template and adds all required packages and references to it.

- Add-PCTService - adds a new service to your project. You can code in this part to handle the endpoint communications. 

- Add-PCTHmiendpoint - creates an endpoint template with HMI commands support and adds all required packages and references to it.

- Add-PCTView - adds a visual interface to your already added HMI endpoint. 

- Add-PCTConfigEndpoint - creates a configuration endpoint template and adds all required packages and references.

- Add-PCTWebHost - creates a web host endpoint template and adds all required packages and references.

## Preparation steps

There are necessary steps to be done before using SDK.Commands:

1. Create an empty class library project
2. Install SDK.Commands.tools to project using nuget packages
3. Open ‘Package Manager Console’ (commands are available only there)

# Commands description
## Add-PCTEndpoint [Name] 

The Add-PCTEndpoint command will add to your project new file 'Endpoint.cs' which contains a basic implementation of 
endpoint class. The command will install all necessary nuget packages and provide logs of installation. It's strongly 
recommended to define only one endpoint per assembly. This command should be used only once per project.

Example:

```
PM> Add-PCTEndpoint MyEndpoint
... (nuget packages installation logs )
Time Elapsed: 00:00:00.5192937
Endpoint class has been successfully added
PM> 
```

If you need to repeat this step to rename your endpoint, please remove the Endpoint.cs from your project first. **You have to create another new class library for every single endpoint.**


![Picture 2](../images/HowToUsePowershellCommandsPic2.png)

Let us explain the changes you can make with an example. 'MyService'  should create an order with an order ID randomly and acts as a  subscriber. All you need to do is to put your code in  ‘SubscribeCommands’ function of 'MyService.cs' as per statement below:



```
 //MyService.cs 
 private void SubscribeCommands()    
 {      
     _commands.On("create_order", (dynamic opts) =>      
     {        
         Random rnd = new Random(DateTime.Now.Millisecond);
         string customer = opts.CustomerName;
         int orderId = rnd.Next(10000, 99999);        
         _logger.Information($"Creating order ID for the customer {customer}, New orderID {orderId}");        
         Thread.Sleep(1000);        
         _commands.Send("order_created", new { OrderId = orderId, CustomerName = customer });      
     });       
     _commands.On("create_order_sync", (dynamic opts) =>      
     {        
         Random rnd = new Random(DateTime.Now.Millisecond);        
         string customer = opts.CustomerName;        
         int orderId = rnd.Next(10000, 99999);        
         _logger.Information($"Creating order ID for the customer {customer}, New orderID {orderId}");        
         Thread.Sleep(1000);        
         return  new { OrderId = orderId, CustomerName = customer };      
     });    
 }
```



You should add some other code to Endpoint class, making sure that your  service will be initialized and loaded. In our example, it should look  like below:

```
//Endpoint.cs 
protected override void OnInitialize(DI di)    
{      
    di.UseProconTELLogger();      
    di.AddModule(new EndpointModule());      
    di.Get<MySampleClassLibraryProject.Services.MyService>().Init();    
}
```

and also add some codes to the Load() function:

```
//Endpoint.cs public override void Load()      
{        
	Bind<MySampleClassLibraryProject.Services.MyService>().ToSelf().InSingletonScope();      
}
```

## Add-PCTService [Name]

The Add-PCTService can be used to add service to existing endpoint. The services represent business logic that can be 
implemented and used by the endpoint. This command can be used multiple times in order to create multiple services.

Example:

```
PM> Add-PCTService MyService
... (nuget packages installation logs )
The service MyService has been successfully added to the project MyEndpoint
PM> 
```

Result project structure:   
![New service added by Add-PCTService command](../images/NewService.PNG)    


## Add-PCTHmiendpoint [Name]

This command should be used for creating endpoint that is able to handle commands from ProconTel user interface view. 
The command doesn't create any view class, it only contains a specific implementation in case of handling commands that
can be called from endpoint status control. If you want to add a view to existing endpoint please use Add-PCTView command. 
After call this command 'Endpoint.cs' file will be created. It's strongly recommended to define only one endpoint per 
assembly. This command should be used only once per project.

Example:

```
PM> Add-PCTHmiendpoint MyHMIEndpoint
... (nuget packages installation logs )
Time Elapsed: 00:00:00.5192937
Endpoint class has been successfully added
PM> 
```

Please be aware of adding additional packages to your project. An overview list can be found here: [List of necessary packages](List_of_packages.md) 


<!--You have to run Add-PCTService [Name] again, to add service.cs to your new HMI project. i.e.-->

<!--PM> Add-PCTService MyHmiService-->  


Now, you have to just add your code to functions as before. Let us continue our example.


```
//Endpoint.cs 
//The changes are the same as before.

//MyHmiService.cs 
//No need to change anything.
```


Since this will show a user interface, you have to create a HMI (frontend). 

![Picture 3](../images/HowToUsePowershellCommandsPic3.png)

Let us take a look at this with the help of an example. Remember our  order example? In order to make our endpoint taking action, we need a  trigger. It can be a simple button as below:

![Picture 4](../images/HowToUsePowershellCommandsPic4.png)

This view will be displayed later on, after you installed the endpoint and added it to a channel or pool. A few changes take place in your additional ViewModel.cs file (if necessary)

```
namespace MyEndpoint.Frontend.MainMenu
{
  [IsDefaultView]
  internal class MyUserControlWPFViewModel : ScreenDialogBase

  {
    private ICommandHub _commands;
    private string orderId;

    public string OrderId
    {
      get => orderId;
      private set
      {
        if (orderId == value)
          return;
        orderId = value;
        NotifyOfPropertyChange(() => OrderId);
      }
    }
    public void MainMenuViewModel(Dispatcher dispatcher, IMainViewModel mainViewModel, ICommandHub commands)
    {
      _commands = commands;
      p = (arg) => OrderId = arg.OrderId;
      _commands.On("order_created", p);
    }
    private Action<dynamic> p;


    public void CreateOrder()
    {
      var response = _commands.Get("create_order_sync", new { CustomerName = "Walczak" });
      OrderId = response.OrderId;
    }
  }
}
```

## Add-PCTView [Name] 

The Add-PCTView command will add to existing endpoint WPF view class with basic implementation. 

The command can be used only after Add-PCTHmiendpoint command.

Example:
```
PM> Add-PCTView SomeView
... (nuget packages installation logs )
Time Elapsed: 00:00:00.4659528
The service MyView has been successfully added to the project MyHMIEndpoint
PM>    
```

Result project structure:   
![New service added by Add-PCTService command](../images/NewView.PNG)    

## Add-PCTConfigEndpoint [Name] 

The Add-PCTConfigEndpoint command will add to your project new file 'Endpoint.cs' which contains basic implementation of 
configuration endpoint class. The command will install all necessary nuget packages and provide logs of installation. It's strongly 
recommended to define only one endpoint per assembly. This command should be used only once per project.

Example:

```
PM> Add-PCTConfigEndpoint MyEndpoint
... (nuget packages installation logs )
Time Elapsed: 00:00:00.5192937
Endpoint class has been successfully added
PM> 
```
## Add-PCTWebHost [Name] [Url]

The Add-PCTWebHost command will add to your project new file 'Endpoint.cs' which contains basic implementation of 
web host endpoint class. The command will install all necessary nuget packages and provide logs of installation. It's strongly 
recommended to define only one endpoint per assembly. This command should be used only once per project.

Example:

```
PM> Add-PCTWebHost WebHost http://localhost:6000
... (nuget packages installation logs )
Time Elapsed: 00:00:00.5192937
Endpoint class has been successfully added
PM> 
```

> If you need more information about Endpoints in general, click here : [Creating an Endpoint](https://macrix.atlassian.net/wiki/spaces/PROC/pages/1762820188/How+to+author+a+custom+endpoint)

