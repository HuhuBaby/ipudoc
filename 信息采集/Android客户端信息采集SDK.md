#Android客户端信息采集SDK.md

##1 功能概述
可以收集移动端的终端信息、终端状态、网络状态、信号信息、地理信息等
##2 sdk集成

###2.1 开启收集器

```java
CollectorConfig config = new Builder(ConfigActivity.this)//此处传入上下文
        .setLocationPeriod(locationPeriod)
        .setTerminalInfoPeriod(infoPeriod)
        .setTerminalStatePeriod(statePeriod)
        .setNetworkPeriod(networkPeriod)
        .setSignalPeriod(signalPeriod).build();
TerminalCollector.getInstance().init(config).start();

```

其中locationPeriod、infoPeriod、statePeriod、networkPeriod、signalPeriod分别是位置信息收集间隔、终端信息收集间隔、终端状态收集间隔、网络信息收集间隔、信号信息收集间隔。

 
 ###2.2 收集终端信息

```java
EventBus.getDefault().unregister(this);

@Subscribe(threadMode = ThreadMode.MAIN)
  public void onInfoEvent(TerminalInfo terminalInfo) {
    String json = JsonUtil.createJson(terminalInfo, TerminalCollector.getInstance().getImei(),terminalInfo.getTopic());
    int type = getActivity().getSharedPreferences(ConfigActivity.PREF_NAME,Context.MODE_PRIVATE).getInt(ConfigActivity.PROTOCOL_TYPE, 1);
    Client.getInstance().send(json,ProtocolType.from(type));
  }

```

###2.3 收集终端状态

```java
EventBus.getDefault().unregister(this);

@Subscribe(threadMode = ThreadMode.MAIN)
  public void onStateEvent(TerminalState terminalState) {
    adapter.addResult(terminalState.toString());
  }

```
###2.3 收集网络状态

```java
EventBus.getDefault().unregister(this);

@Subscribe(threadMode = ThreadMode.MAIN)
  public void onNetworkEvent(NetworkInfo info) {
    adapter.addResult(info.toString());
  }

```
###2.4 收集信号信息

```java
EventBus.getDefault().unregister(this);

@Subscribe(threadMode = ThreadMode.MAIN)
  public void onSignalEvent(CellGeneralInfo cellGeneralInfo) {
    adapter.addResult(cellGeneralInfo.toString());

  }

```
###2.5 收集地理信息

```java
EventBus.getDefault().unregister(this);

@Subscribe(threadMode = ThreadMode.MAIN)
    public void onLocationEvent(TcLocation location) {
        double latitude = location.getLatitude();
        double longitude = location.getLongitude();
      
    }

```
