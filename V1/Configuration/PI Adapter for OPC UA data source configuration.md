---
uid: PIAdapterForOPCUADataSourceConfiguration
---

# PI Adapter for OPC UA data source configuration

To use the adapter, you must configure the data source from which it polls data.

## Configure OPC UA data source

**Note:** To modify OPC UA data source configuration, you must use the REST endpoints to add or edit the configuration.

Complete the following steps to configure an OPC UA data source:

1. Use any text editor to create a file that contains an OPC UA data source in the JSON format.
    - For content structure, see [OPC UA data source examples](#opc-ua-data-source-examples).
    - For a table of all available parameters, see [OPC UA data source parameters](#opc-ua-data-source-parameters).
2. Save the file. For example, `ConfigureDataSource.json`.
3. Use any of the [Configuration tools](xref:ConfigurationTools) capable of making HTTP requests to run a PUT command with the contents of that file to the following endpoint: `http://localhost:5590/api/v1/configuration/<adapterId>/DataSource/`.

      **Note:** The following example uses OpcUa1 as the adapter component name. For more information on how to add a component, see [System components configuration](xref:SystemComponentsConfiguration).

    `5590` is the default port number. If you selected a different port number, replace it with that value.

    Example using `curl`:

    ```bash
    curl -d "@ConfigureDataSource.json" -H "Content-Type: application/json" -X PUT "http://localhost:5590/api/v1/configuration/OpcUa1/DataSource"
    ```

    **Note:** Run this command from the same directory where the file is located.

4. Configure data selection. For more information, see [PI Adapter for OPC UA data selection configuration](xref:PIAdapterForOPCUADataSelectionConfiguration).

    **Note:** You can decide to have a default data selection file generated automatically or you can create the data selection file yourself.

## OPC UA data source schema

The full schema definition for the OPC UA data source configuration is in the `OpcUa_DataSource_schema.json` file located in one of the following folders:

Windows: `%ProgramFiles%\OSIsoft\Adapters\OpcUa\Schemas`

Linux: `/opt/OSIsoft/Adapters/OpcUa/Schemas`

## OPC UA data source parameters

The following parameters are available for configuring an OPC UA data source:

| Parameter | Required | Type | Description |
|-----------|----------|------|-------------|
| **EndpointURL** | Required | `string` | The endpoint URL of the OPC UA server in opc.tcp format. The following is an example of the URL format: opc.tcp://OPCServerHost:Port/OpcUa/SimulationServer<br><br>**Note:** If you change the EndpointURL on a configured adapter that has _ComponentID_DataSelection_ .json file exported, you need to remove the _ComponentID_DataSelection.json_ file from the configuration directory to trigger a new browse (export). <br><br>Allowed value: well-formed opc.tcp address|
| **UseSecureConnection**|Optional | `boolean` | When set to true, the adapter connects to a secure endpoint using OPC UA certificate exchange operation. The default is true. When set to false, the adapter connects to an unsecured endpoint of the server and certificate exchange operation is not required.<br><br>**Note:** OSIsoft recommends setting this option to false for testing purposes only. <br><br> Allowed value: `true` or `false`<br>Default value: `true`|
| **UserName** | Optional | `string` | User name for accessing the OPC UA server. <br><br>Allowed value: any string <br> Default value: `null`|
| **Password** | Optional | `string` | Password for accessing the OPC UA server.<br><br>**Note:** OSIsoft recommends using REST to configure the data source when the password must be specified because REST will encrypt the password. If you do not use REST, the plain text password will be stored on-disk. <br><br>Allowed value: any string <br> Default value: `null`|
| **RootNodeIds** | Optional | `string` | List of comma-separated NodeIds of those objects from which the adapter browses the OPC UA server address space. This option allows to select only subsets of the OPC UA address by explicitly listing one or more NodeIds which are used to start the initial browse.<br><br>Examples:<br>`"ns=5;s=85/0:Simulation"`<br>`"ns=3;s=DataItems"`<br><br>If not specified, it means that the whole address space will be browsed. <br><br>Allowed value: comma-separated collection of node Ids<br> Default value: `null`|
| **IncomingTimestamp** | Optional | `string` | Specifies whether the incoming timestamp is taken from the source, from the OPC UA server, or should be created by the adapter instance. <br> **Source** - Default and recommended setting. The timestamp is taken from the source timestamp field. The source is what provides data for the item to the OPC UA server, such as a field device.<br> **Server** - In case the OPC UA item has an invalid source timestamp field, the Server timestamp can be used.<br> **Adapter** - The adapter generates a timestamp for the item upon receiving it from the OPC UA server. <br><br>Allowed value: `Source`, `Server`, or `Adapter` <br> Default value: `Source`|
| **StreamIdPrefix** | Optional | `string` | Specifies what prefix is used for Stream IDs. The naming convention is StreamIdPrefix.StreamId. An empty string means no prefix will be added to the Stream IDs and names. Null value defaults to ComponentID followed by a dot, for example, *OpcUa1*.NamespaceIndex.Identifier.<br><br>**Note:** Every time you change the StreamIdPrefix of a configured adapter, for example when you delete and add a data source, you need to restart the adapter for the changes to take place. New streams are created on adapter restart and pre-existing streams are no longer updated. <br><br>Allowed value: any string <br> Default value: `null`|
| **DefaultStreamIdPattern** | Optional | `string` | Specifies the default stream Id pattern to use. Possible parameters: {NamespaceIndex}<sup>1</sup>, {Identifier}, {Name}. <br><br>Allowed value: any string<br>Default value: `{NamespaceIndex}.{Identifier}` |

<sup>1</sup> refers to the number specified in the `ns` keyword in the **RootNodeIds** parameter.

## OPC UA data source examples

The following are examples of valid OPC UA data source configurations:

### Minimum data source configuration

```json
{
    "EndpointUrl": "opc.tcp://<IP-Address>:<Port>/<TestOPCUAServer>"
}
```

### Maximum data source configuration

```json
{
    "EndpointUrl": "opc.tcp://<IP-Address>:<Port>/<TestOPCUAServer>",
    "UseSecureConnection": true,
    "UserName": null,
    "Password": null,
    "RootNodeIds": null,
    "IncomingTimestamp": "Source",
    "StreamIdPrefix": null,
    "DefaultStreamIdPattern": "{NamespaceIndex}.{Identifier}"
}
```

## REST URLs

| Relative URL | HTTP verb | Action |
| ------------ | --------- | ------ |
| api/v1/configuration/_ComponentId_/DataSource  | GET | Retrieves the OPC UA data source configuration |
| api/v1/configuration/_ComponentId_/DataSource  | POST | Creates the OPC UA data source configuration |
| api/v1/configuration/_ComponentId_/DataSource  | PUT | Configures or updates the OPC UA data source configuration |
| api/v1/configuration/_ComponentId_/DataSource | DELETE | Deletes the OPC UA data source configuration |

**Note:** Replace _ComponentId_ with the Id of your OPC UA component, for example OpcUa1.
