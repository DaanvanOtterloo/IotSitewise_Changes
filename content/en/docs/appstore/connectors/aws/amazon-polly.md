---
title: "Amazon Polly"
url: /appstore/connectors/aws/amazon-polly/
category: "Connectors"
description: "Describes the configuration and usage of the Amazon Polly connector, which is available in the Mendix Marketplace. Amazon Polly uses deep learning technologies to synthesize natural-sounding human speech, so you can convert articles to speech."
weight: 20
tags: ["marketplace", "marketplace component", "aws", "polly", "connector", "amazon"]
aliases:
    - /appstore/connectors/amazon-polly/
#If moving or renaming this doc file, implement a temporary redirect and let the respective team know they should update the URL in the product. See Mapping to Products for more details. 
---

## 1 Introduction

The [Amazon Polly](https://marketplace.mendix.com/link/component/205068) connector enables you to connect your app to [Amazon Polly](https://aws.amazon.com/polly/) and easily convert written text into human voice in your web applications.

### 1.1 Typical Use Cases

Amazon Polly allows you to synthesize text to speech, with 96 voices supporting 34 languages. With dozens of lifelike voices across a broad set of languages, use Amazon Polly to build speech-activated applications, for example, to accommodate the following use cases:

* Provide accessibility features for users who have difficulty reading
* Create audio-supported interfaces in your application.

### 1.2 Prerequisites {#prerequisites}

The Amazon Polly connector requires Mendix Studio Pro version 9.18.0 or above.

To authenticate with Amazon Web Service (AWS), you must also install and configure the [AWS authentication connector version 2.1 or higher](https://marketplace.mendix.com/link/component/120333). For more information about installing and configuring the AWS Authentication connector, see [AWS Authentication](/appstore/connectors/aws/aws-authentication/).

### 1.3 Licensing and Cost

This connector is available as a free download from the Mendix Marketplace, but the AWS service to which is connects may incur a usage cost. For more information, refer to AWS documentation.

{{% alert color="info" %}}
Most AWS services provide a free tier that allows easy access to most services. To find out if this service is included in the free tier, see [AWS Free Tier](https://aws.amazon.com/free/). To calculate the potential cost of using an AWS service outside of the free tier, use the [AWS Cost calculator](https://calculator.aws/).
{{% /alert %}}

Depending on your use case, your deployment environment, and the type of app that you want to build, you may also need a license for your Mendix app. For more information, refer to [Licensing Apps](/developerportal/deploy/licensing-apps-outside-mxcloud/).

## 2 Installation

Follow the instructions in [How to Use Marketplace Content in Studio Pro](/appstore/general/app-store-content/) to import the Amazon Polly connector into your app.

## 3 Configuration

After you install the connector, you can find it in the **App Explorer**, in the **AmazonPollyConnector** section. The connector provides a domain model and activities that you can use to connect your app to Amazon Polly. Each activity can be implemented using it in a microflow or nanoflow. To ensure that your app can connect to the AWS service, you must also configure AWS authentication for the connector.

### 3.1 Configuring AWS Authentication {#authentication}

In order to use the Amazon Polly service, you must authenticate with AWS. To do so, you must set up a configuration profile in your Mendix app. After you set up the configuration profile, the connector module handles the authentication internally.

1. Ensure that you have installed and configured the AWS Authentication connector, as mentioned in [Prerequisites](#prerequisites).
2. Decide whether you want to use temporary or static credentials to authenticate.
    The Amazon Polly connector supports both temporary and static credentials. By default, the connector is pre-configured to use static credentials, but you may want to switch to temporary credentials, for example, to increase the security of your app. For an overview of both authentication methods, see [AWS Authentication](/appstore/connectors/aws/aws-authentication/).
3. In the **App Explorer**, double-click the **Settings** for your app.

    {{< figure src="/attachments/appstore/connectors/aws-polly/polly_open_settings.png" alt="The Settings option in the App Explorer">}}

4. In the **App Settings** dialog, in the **Configurations** tab, edit or create an authentication profile.
    If you have multiple sets of AWS credentials, or if you want to use both static and temporary credentials for different use cases, create separate authentication profiles for each set of credentials.
5. In the **Edit Configuration** dialog, in the **Constants** tab, click **New** to add the constants required for the configuration.
6. In the **Select Constants** dialog, find and expand the **AmazonPollyConnector** > **ConnectionDetails** section.

    {{< figure src="/attachments/appstore/connectors/aws-polly/polly_edit_configuration.png" alt="The temporaryCredentials and StaticCredentials items in the ConnectionDetails section">}}

7. Depending on your selected authentication type, configure the required parameters for the **StaticCredentials** or **TemporaryCredentials**.

    | Credentials type | Constant | Value |
    | --- | --- | --- |
    | Any | **UseStaticCredentials** | **true** if you want to use static credentials, or **false** for temporary credentials |
    | **StaticCredentials** | **AccessKey** | Access key ID [created in IAM](/appstore/connectors/aws/aws-authentication/#prerequisites)  |
    | **StaticCredentials** | **SecretKey** | Secret key [created in IAM](/appstore/connectors/aws/aws-authentication/#prerequisites) |
    | **TemporaryCredentials** | **Role ARN** | [ARN](https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html) of the AWS role that the connector should assume |
    | **TemporaryCredentials** | **Profile ARN** | ARN of the profile [created in IAM Roles Anywhere](/appstore/connectors/aws/aws-authentication/#prerequisites) |
    | **TemporaryCredentials** | **Trust Anchor ARN** | ARN of the trust anchor [created in IAM Roles Anywhere](/appstore/connectors/aws/aws-authentication/#prerequisites) |
    | **TemporaryCredentials** | **Client Certificate Identifier** | The **Client Certificate Pin** visible in the **Outgoing Certificates** section on the **Network** tab in the Mendix Cloud environment |
    | **TemporaryCredentials** | **Duration** | Duration for which the session token should be valid; after the duration passes, the validity of the temporary credentials expires |
    | **TemporaryCredentials** | **Session Name** | An identifier for the session |

### 3.2 Configuring a Microflow for an AWS Service

After you configure the authentication profile for Amazon Polly, you can implement the functions of the connector by using the provided activities in microflows. For example, to synthesize a string of text into speech, implement the [SynthesizeSpeech](#synthesize-speech) activity by performing the following steps:

1. In the **App Explorer**, right-click on the name of your module, and then click **Add microflow**.
2. Enter a name for your microflow, for example, *ACT_SynthesizeSpeech*, and then click **OK**.
3. In the **App Explorer**, in the **AmazonPollyConnector** section, find the **SynthesizeSpeech** operation microflow.
4. In the **App Explorer**, in the **AWSAuthentication** section, find the **GenerateCredentials** operation microflow.
5. Drag the **GenerateCredentials** and  **SynthesizeSpeech** operation microflow's in to your microflow in that order.
6. Double-click the **SynthesizeSpeech** activity to configure the required parameters. 
    
    For the `SynthesizeSpeech` activity, you must add your own entity that inherits from `System.FileDocument` to store the contents of the audio, provide a credentials object and specify the AWS Region. You must then add your `SynthesizeSpeechRequest` entity in your microflow as the last parameter. This entity requires the following parameters:

    * `Text` - The actual string of text that needs to be synthesized.
    * `OutputFormat` - MP3, OGG_VORBIS, or PCM. JSON is not yet supported by the connector.
    * `Engine` - The enumeration parameter that specifies which engine (*standard* or *neural*) will be used by Amazon Polly during speech synthesis. It is set automatically to *standard* in the connector when a `SynthesizeSpeechRequest` object is created, but can be modified by the user.
    * `VoiceID` - The selected voice for the output of the text.

    The following parameters are optional:
    * `TextType` - The enumeration parameter to specify if the input text is plain text or SSML. If left empty, the default value is plain text.
    * `SampleRate` - The valid values for MP3 and OGG_VORBIS are *8000*, *16000*, *22050*, and *24000*. The valid values for PCM are *8000* and *16000*. The default values are used by Amazon Polly, if not given.
    * `LanguageCode` - Language code of the voice. This parameter is only necessary if you are using a voice that can use multiple languages. If left empty, the default value for that voice is used. 
    
    For more information on Amazon Polly voices and their engines, see [Voices in Amazon Polly](https://docs.aws.amazon.com/polly/latest/dg/voicelist.html).
7. In the **Edit parameters** section, edit the **AWS_Region** parameter, and provide a value by using a variable or an expression. For a list of available AWS regions, see [AWS_Region](#aws-region).
8. Click **OK**.
9. Open a page that contains a widget for playing audio, for example, the [Play Audio](https://marketplace.mendix.com/link/component/120804) widget.
10. Configure the widget to use the `FileDocument` configured in the **SynthesizeSpeech** activity.
11. Configure a method to trigger the `ACT_SynthesizeSpeech` activity. 
    For example, you can associate the activity with a custom button on a page in your app. For an example of how this can be implemented, see [Creating a Custom Save Button with a Microflow](/refguide/creating-a-custom-save-button/).
    
## 4 Technical Reference

To help you work with the Amazon Polly connector, the following sections of this document list the available entities, activities, and enumerations that you can use in your application.

### 4.1 Domain Model

The domain model is a data model that describes the information in your application domain in an abstract way. For more information, see [Domain Model](/refguide/domain-model/).

#### 4.1.1 AbstractVoice {#abstractvoice}

The `AbstractVoice` entity is a representation of an Amazon Polly voice. It contains the unique identifier for a voice (`VoiceID`) that is required for `SynthesizeSpeech` activities. It also contains additional information on a voice like the name, language and gender.

| Attribute | Description |
| --- | --- |
| `VoiceID` | ID assigned by Amazon Polly that is specified when calling the [SynthesizeSpeech](#synthesize-speech) action (string)|
| `Name` | Name of the voice (string)|
| `LanguageName` | Name of the language in English (string)|
| `LanguageCode` | The language code of the voice (string)|
| `Gender` | Gender of the voice. (string)|

#### 4.1.2 DescribeVoicesRequest

The `DescribeVoicesRequest` is the request object used for the  [DescribeVoices](#describe-voices) action. It inherits from the `AbstractRequest` entity (AWSAuthentication v.3.0.0).

#### 4.1.3 DescribeVoicesResponse

The `DescribeVoicesResponse` entity is the response for the [DescribeVoices](#describe-voices) action. The action also returns a list of `DescribeVoice` objects associated with the `DescribeVoicesResponse` object. The `DescribeVoice` entity inherits from the voice entity and contains all its attributes. For more information on the attributes, see [Voice](#voice).

#### 4.1.4 SynthesizeSpeechRequest

The `SynthesizeSpeechRequest` entity is used when calling the [SynthesizeSpeech](#synthesize-speech) action. 

| Attribute | Description |
| --- | --- |
| `Text` | Text to synthesize (string)|
| `TextType` | It is used to specify whether the input text is plain text or SSML. If left empty, the default value is 'plain text' (enum)|
| `OutputFormat` | The format that the output should be encoded (enum)|
| `SampleRate` | The sample rate for speech synthesis (string)|
| `Engine` | The engine that will be used during speech synthesis (enum)|
| `VoiceID` | The ID of the voice (string)|
| `LanguageCode` | The language code of the voice (string)|

### 4.2 Enumerations

An enumeration is a predefined list of values that an be used as an attribute type.

#### 4.2.1 `OutputFormat`

The format in which the returned output will be encoded. For audio streams, this must be MP3, OGG_VORBIS, or PCM. JSON is currently not supported.

| Name | Caption |
| --- | --- |
| MP3        | MP3        |
| OGG_VORBIS | OGG_VORBIS |
| PCM        | PCM        |

#### 4.2.2 `Engine`

Specifies the engines (standard or neural) that are supported by a given voice.

| Name | Caption |
| --- | --- |
| Standard | standard |
| Neural | neural |

#### 4.2.3 `TextType`

| Name | Caption |
| --- | --- |
| PlainText | PlainText |
| SSML | SSML |

### 4.3 Activities

Activities define the actions that are executed in a microflow or a nanoflow.

#### 4.3.1 Synthesize Speech {#synthesize-speech}

The `SynthesizeSpeech` activity allows you to synthesize a string of text into an audio file. It requires a `Credentials` object, a `SynthesizeSpeechRequest` object containing the required information, and a `TargetFileDocument` (`System.FileDocument` or its specialization) to contain the response of the `SynthesizeSpeech` activity. It also requires the `AWS_Region` in which the Polly service should be called.

The `SynthesizeSpeech` activity has no return value, instead the input parameter TargetFileDocument contains the synthesized speech.

#### 4.3.2 DescribeVoices {#describe-voices}

The `DescribeVoices` activity returns the list of voices from the Amazon Polly service. It requires a `Credentials` object and an `AWS_Region` parameter to get the list of supported voices from the given AWS region.
The voices can be used in the `Synthesize Speech` activity to synthesize a piece of text to speech with the right voice. 

The `DescribeVoices` activity returns a `DescribeVoicesResponse` entity containing a list of `DescribeVoice` entities. Examples of the returned `DescribeVoice` objects can be seen below. 

 {{< figure src="/attachments/appstore/connectors/aws-polly/polly-example-voices.png" alt="Examples of available voices for Amazon Polly">}}

For more Amazon Polly voices and their information, see [Voices in Amazon Polly](https://docs.aws.amazon.com/polly/latest/dg/voicelist.html).
