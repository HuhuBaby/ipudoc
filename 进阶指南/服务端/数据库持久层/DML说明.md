# DML说明

#增

```java

public boolean insertInfos(IDataset infos) throws Exception{

    IData data = infos.getData(0);

    boolean resultFlag = this.insert("IpuMemberInfo", data);

    if (log.isDebugEnabled()){

        log.debug(resultFlag ? "数据插入成功" : "数据插入失败");

    }

    return resultFlag;

}

```

#删

```java

public boolean deleteInfos(IDataset staffid) throws Exception{

    IData data = staffid.getData(0);

    boolean resultFlag =  this.delete("IpuMemberInfo", data , new String[]{ "STAFFID" });

    if (log.isDebugEnabled()){

        log.debug(resultFlag ? "数据删除成功" : "数据删除失败");

    }

    return resultFlag;

}

```

#改

```java

public boolean updateInfos(IDataset infos) throws Exception{

    IData data = infos.getData(0);

    boolean resultFlag = this.update("IpuMemberInfo", data, new String[]{"STAFFID"});

    if (log.isDebugEnabled()){

        log.debug(resultFlag ? "数据更新成功" : "数据更新失败");

    }

    return resultFlag;

}

```

#查

```java

public DatasetList queryInfos(IData params) {

    DatasetList infos = new DatasetList();

        IData datas = this.queryList("select * from IpuMemberInfo s where s.STAFFID = :StaffID",params).first();

        if (log.isDebugEnabled()){

            log.debug("查询到的数据为" + datas == null ? "null" : datas.toString());

        }

    return datas;

}

```

