---
title: "Generate Report Button"
url: /refguide8/report-button/
weight: 40
tags: ["studio pro"]
#If moving or renaming this doc file, implement a temporary redirect and let the respective team know they should update the URL in the product. See Mapping to Products for more details.
---

{{% alert color="info" %}}
<img src="/attachments/china.png" class="d-inline-block" /> For the Simplified Chinese translation, click [中文译文](https://cdn.mendix.tencent-cloud.com/documentation/refguide8/report-button.pdf).
{{% /alert %}}

## 1 Introduction

You can set a [report grid](/refguide8/report-grid/) to automatically generate the report when the page is loaded. However, if a report page has parameter widgets, you must also add a **Generate report button** widget so that the end-user can regenerate the report after specifying the parameter(s).

The reports are generated when the end-user clicks the generate report button.

## 2 Report Parameter Properties

An example of generate report button properties is represented in the image below:

{{< figure src="/attachments/refguide8/modeling/pages/report-widgets/report-button/generate-report-button-properties.png" alt="Report parameter in structure mode"   width="300"  >}}

Generate report button properties consist of the following sections:

* [Common](#common)
* [Design Properties](#design-properties)
* [General](#general)

### 2.1 Common Section{#common}

{{% snippet file="/static/_includes/refguide8/common-section-link.md" %}}

### 2.2 Design Properties Section{#design-properties}

{{% snippet file="/static/_includes/refguide8/design-section-link.md" %}}

### 2.3 General Section{#general}

#### 2.3.1 Caption

**Caption** is the string that the end-user sees on the button that generates reports with the chosen parameters.
