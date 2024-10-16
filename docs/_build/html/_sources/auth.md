# Authentication in Azure Synapse

Two types of authentication are supported for serverless SQL pools:

- SQL Authentication (username, password)
- Microsoft Entra authentication

## Authorization within a serverless SQL pool

With SQL authentication, the user exists only in the serverless SQL pool. With Microsoft Entra, the user can sign in to
a serverless SQL pool **and** other services.

## Access to storage accounts

A user can be authorized to access and query files in Azure Storage. Serverless SQL pools supports following
authorization types, all of which are supported by Microsoft Entra:

- Anonymous access
- Shared access signature (SAS, SQL user supported)
- Managed Identity
- User Identity

To give admin privileges to a user in Synapse, select the _Synapse Administrator_ role within `Access control`.

## Azure access

Assign roles of type _Reader_ for users with **read** only access, and _Contributor_ for users which need **read/write**
access. E.g. when users need to creat external tables as select (CETAS).
