---
layout: post
title: Delegate Event and Observer pattern
modified: 2017-1-31
categories: C#
tags: [Delegate, Event, Design pattern]
comments: true
---


### Delegate
To be continue...
Test code highlighting.
Test code highlighting.
Test code highlighting.
<pre >
/// <summary>
/// Modify.
/// </summary>
[TestMethod()]
[RollBack()]
public void UpdateBondAssessApplicationConfiguration_ModifyData_DataUpdated() //注意命名规范，下一篇会着重讲解！
 {
	AssessApplicationConfigurationDataSet assessConfigurationDataSet = target.GetBondAssessApplicationConfiguration();
	assessConfigurationDataSet.CM_LookupConfiguration[0].Description = "This is my modify";
	AssessApplicationConfigurationDataSet actual = target.UpdateBondAssessApplicationConfiguration(assessConfigurationDataSet);
	AssessApplicationConfigurationDataSet newAssessAppDataSet = target.GetBondAssessApplicationConfiguration();
	
	Assert.IsNotNull(actual);                       //第一步 验证是否为Null
	Assert.IsTrue(actual.CM_LookupConfiguration.Rows.Count > 0);       //第二步 验证是否为Empty
	Assert.IsTrue(newAssessAppDataSet.CM_LookupConfiguration[0].Description == "This is my modify");//第三步 验证数据的一致性
	Assert.IsTrue(CompareToTable(newAssessAppDataSet.CM_LookupConfiguration, actual.CM_LookupConfiguration));
}
</pre>

Test code highlighting.
Test code highlighting.
Test code highlighting.
Test code highlighting.

### Event



### Observer pattern





