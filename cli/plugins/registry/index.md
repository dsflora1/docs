---



copyright:

  years: 2017

lastupdated: "2017-03-20"


---

{:codeblock: .codeblock}
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# {{site.data.keyword.registrylong_notm}} CLI
{: #containerregcli}

The {{site.data.keyword.registrylong}} CLI is a plug-in that lets you manage your registry resources, such as namespaces and images, across your org.
{: shortdesc}

**Prerequisites**
* Before running the registry commands, log in to {{site.data.keyword.Bluemix_short}}
 with the `bx login` command to generate a {{site.data.keyword.Bluemix_short}}
 access token and authenticate your session.

<table summary="Manage your Containers Registry">
<caption>Table 1. Commands for managing {{site.data.keyword.registryshort}} on {{site.data.keyword.Bluemix_short}}
</caption>
 <thead>
 <th colspan="5">Commands for managing the registry</th>
 </thead>
 <tbody>
 <tr>
 <td>[bx cr api](#bx-cr-api)</td>
 <td>[bx cr info](#bx-cr-info)</td>
 <td>[bx cr image-inspect](#bx-cr-image-inspect)</td>
 <td>[bx cr image-list](#bx-cr-image-list)</td>
 <td>[bx cr image-rm](#bx-cr-image-rm)</td>
 </tr>
 <tr>
 <td>[bx cr login](#bx-cr-login)</td>
 <td>[bx cr namespace-add](#bx-cr-namespace-add)</td>
 <td>[bx cr namespace-list](#bx-cr-namespace-list)</td>
 <td>[bx cr namespace-rm](#bx-cr-namespace-rm)</td>
 </tr></tbody></table>


## bx cr api
Returns the details about the registry API endpoint that the commands are run against.

```
bx cr api
```
{: codeblock}


## bx cr info
Displays the name and the org of the registry that you are logged in to.

```
bx cr info
```
{: codeblock}


## bx cr image-inspect
View details about a specific image.

```
bx cr image-inspect [--format FORMAT] IMAGE [IMAGE]
```
{: codeblock}

**Parameters**
<dl>
<dt>--format FORMAT</dt>
<dd>(Optional) Format the output elements by using a Go template. For examples, see [Viewing information](https://console.bluemix.net/docs/services/Registry/registry_cli_listing.html).</dd>
<dt>IMAGE</dt>
<dd>The full {{site.data.keyword.Bluemix_short}} registry path to the image that you want to inspect. If a tag is not specified in the image path, the image tagged `latest` is inspected. You can inspect multiple images by listing each private {{site.data.keyword.Bluemix_short}} registry path in the command with a space between each path.</dd>
</dl>


## bx cr image-list
View all images in your {{site.data.keyword.Bluemix_short}} org.

```
 bx cr image-list [--no-trunc] [-q, --quiet] [--include-ibm] [--format FORMAT]
```
{: codeblock}

**Parameters**
<dl>
<dt>--no-trunc</dt>
<dd>(Optional) Do not truncate the output.</dd>
<dt>-q, --quiet</dt>
<dd>(Optional) Displays a unique identifier for the image in the format: 'repository:tag'.</dd>
<dt>--include-ibm</dt>
<dd>(Optional) Includes IBM-provided public images in the output. Without this option, private images only are listed.</dd>
<dt>--format FORMAT</dt>
<dd>(Optional) Format the output elements by using a Go template. For examples, see [Viewing information](https://console.bluemix.net/docs/services/Registry/registry_cli_listing.html).</dd>
</dl>


## bx cr image-rm
Delete a specified image from your registry.

```
bx cr image-rm IMAGE [IMAGE]
```
{: codeblock}

**Parameters**
<dl>
<dt>IMAGE</dt>
<dd>The full {{site.data.keyword.Bluemix_short}} registry path to the image that you want to remove. If a tag is not specified in the image path, the image tagged `latest` is deleted by default. You can delete multiple images by listing each private {{site.data.keyword.Bluemix_short}} registry path in the command with a space between each path.</dd>
</dl>


## bx cr login
If Docker is installed, this command runs the `docker login` command against the registry. The `docker login` command is required to be able to run the `docker push` or `docker pull` commands for the registry. This command is not required to run other `bx cr` commands. If Docker is not installed, this command returns an error message.

```
bx cr login
```
{: codeblock}


## bx cr namespace-add
Adds a namespace to your Bluemix org. 

```
bx cr namespace-add NAMESPACE
```
{: codeblock}

**Parameters**
<dl>
<dt>NAMESPACE</dt>
<dd>The namespace you want to add. The namespace must be unique across all {{site.data.keyword.Bluemix_short}} orgs.</dd>
</dl>


## bx cr namespace-list
View all namespaces of your {{site.data.keyword.Bluemix_short}} org.

```
bx cr namespace-list
```
{: codeblock}


## bx cr namespace-rm
Removes a namespace from your {{site.data.keyword.Bluemix_short}}  org. Images in this namespace are deleted when the namespace is removed.

```
bx cr namespace-rm NAMESPACE
```
{: codeblock}

**Parameters**
<dl>
<dt>NAMESPACE</dt>
<dd>The namespace that you want to remove.</dd>
</dl>
