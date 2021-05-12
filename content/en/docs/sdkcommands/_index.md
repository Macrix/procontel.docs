---
Title: "SDK.Commands"
Weight: 8
Description: >
  Learn how to use the ProconTEL Monitoring
---

# SDK.Commands

## Table of Contents

1. [Quick introduction](#id-quick-introduction)
2. [Compatibility matrix](#id-compatibility-matrix)
3. [Additional documentations](#id-additional-documentations)

<div id='id-quick-introduction'/>

## 1. Quick introduction

`SDK.Commands` is a modern .NET Standard SDK for porting your business logic with [ProconTEL](http://procontel.com/) environment. The modular design and middleware oriented architecture makes the endpoint highly customizable while providing sensible default for topology, communication and extensions. Documentation for version 1.x is currently available under [`docs`](https://macrix.eu/).

<div id='id-compatibility-matrix'/>

## 2. Compatibility matrix
As SDK version may change, we provide SDK.Commands compatibility matrix which shows which SDK.Commands versions is supported by which *ProconTEL Engine* and *ProconTEL SDK*.
| *ProconTEL Engine* | *ProconTEL SDK* | *SDK.Commands* | 
| :---:  |:---:|:---:|
| 3.0.13 | 1.0.2 | 1.3.0 |
| 3.0.12 | 1.0.1 | 1.2.0 |
| 3.0.10 | 1.0.0 | 1.1.0 |
| 3.0.9 | 1.0.0 | 1.1.0 |

<div id='id-additional-documentations'/>

## 3. Additional documentation


- ICommand_hub

```
ICommandHub describes a way of communication in Procontel ADP solutions. 
```

- List_of_packages

```
List of necessary packages
```

- Using_of_Powershell_Commands

```
Available commands,
Add-PCTEndpoint,
Add-PCTService,
Add-PCTHmiendpoint,
Add-PCTView,
Add-PCTConfigEndpoint
```

- HowToRelease

```
Tag patterns,
Release SDK.Commands,
Release version Sdk.Commands.Tools
```