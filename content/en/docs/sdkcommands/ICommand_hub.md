---
Title: "ICommandHub"
Weight: 8
Description: >
  Learn about ICommandHub
---

## How to use ICommandHub
ICommandHub describes a way of communication in Procontel ADP solutions. The basic idea behind it is to give one common interface that can be used in communication between two backend services (endpoint service to endpoint service) as well as between backend and frontend of service (endpoint service and status control of the endpoint).  



ADP provides two ICommandHub implementations, that should be inherited by endpoint class:

- CommandsAwareEndpoint - which support backend to backend communication

- ClientSideHmiCommandHub - which extends CommandsAwareEndpoint by frontend to backend communication support

  

The communications rely on registered commands that are callable from any point of the system i.e. command on backend called from frontend or command from one endpoint service called from another endpoint. Each command has to have a unique identifier called commandId. In purpose of registering the command On(...)method should be used.  There are all available registration methods:



```
/// Registration of method returning void
void On<TCommand>(string commandId, Action<TCommand> action);

/// Registration of method returning Task
void On<TCommand>(string commandId, Func<TCommand, Task> action);

/// Registration of method returning any strong-typed result
void On<TCommand, TResult>(string commandId, Func<TCommand, TResult> action);

/// Registration of method returning any strong-typed Task
void On<TCommand, TResult>(string commandId, Func<TCommand, Task<TResult>> action);

/// Awaitable versions of above methods    
Task OnAsync<TCommand>(string commandId, Action<TCommand> action);
Task OnAsync<TCommand>(string commandId, Func<TCommand, Task> action);
Task OnAsync<TCommand, TResult>(string commandId, Func<TCommand, TResult> action);
Task OnAsync<TCommand, TResult>(string commandId, Func<TCommand, Task<TResult>> action);
```



There are two ways to call the registered commands, both available in strong-type and dynamic versions: 

- Post method that calls command and never return back any value. If the registered command provides any response it will be discarded.

```
void Post(string commandId, dynamic message);
void Post<TMessage>(string commandId, TMessage message);
Task PostAsync(string commandId, dynamic message);
Task PostAsync<TMessage>(string commandId, TMessage message);
```

- Get method that  always returns a value. If the command doesn’t provide any response,  returned value will be a default for given type or null in case of  dynamic version. 

```
dynamic Get(string commandId, dynamic message);
TResult Get<TMessage, TResult>(string commandId, TMessage message);
Task<dynamic> GetAsync(string commandId, dynamic message);
Task<TResult> GetAsync<TMessage, TResult>(string commandId, TMessage message);
```



Example:

```
/// server side code, _commands is ICommandHub type
...
_commands.On("create_order", (dynamic opts) =>
{
  Random rnd = new Random(DateTime.Now.Millisecond);
  string customer = opts.CustomerName;
  int orderId = rnd.Next(10000, 99999);
  return new { OrderId = orderId, CustomerName = customer };
});
...
```

```
/// ui side code
public async void CreateOrder()
{
  var commandId = "create_order";
  var payload = new { CustomerName = "Kowalski" };
  var response = await _commands.GetAsync(commandId, payload);
}
```



If you looking for more advanced example please visit our gitlab page https://gitlab.macrix.eu/pctext/ADP and investigate our samples on you own.