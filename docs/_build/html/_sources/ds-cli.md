# Azure CLI with ML

Install Azure ML using the `ml` extension as follows:

```bash
az extension add -n ml -y
```

## Help with CLI commands

To show a list of commands, use the `-h` flag:

```bash
az ml -h
```

## Working with Azure ML from the CLI

For example, to create a compute target, you can use the following command:

```bash
az ml compute create --name aml-cluster --size STANDARD_DS3_v2 --min-instances 0 --max-instances 5 --type AmlCompute --resource-group my-resource-group --workspace-name my-workspace
```

Alternative, put configuration inside a YAML file:

```yaml
$schema: https://azuremlschemas.azureedge.net/latest/amlCompute.schema.json
name: aml-cluster
type: amlcompute
size: STANDARD_DS3_v2
min_instances: 0
max_instances: 5
```

and run:

```bash
az ml compute create --file compute.yml --resource-group my-resource-group --workspace-name my-workspace
```