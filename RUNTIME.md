# Updating the runtime object

This document refers to the Runtime interface defined in packages/teams-js/src/public/runtime.ts. The runtime object describes the capabilities and configuration of the host and is versioned so that the teams-js library may parse older versions of the runtime. The following information describes when and how the Runtime version should be updated.

## What constitutes a major version change?

A major version change is one by which it is impossible to either upgrade from the previous version to the next version or downgrade from the next version to the previous version without losing functionality. For example, if capabilities x and y are merged into a new capability z, then we cannot determine if z is supported in the new version unless both x and y are supported in the previous version. If x but not y was supported in the previous version, then z must be defined as unsupported in the next version and all functionality covered under x is lost despite no changes being made to the host’s functionality. Similarly, if a capability is split into two different capabilities then we cannot determine if that capability in the previous version is supported based on the capabilities in the next version.

## What constitutes a minor version change?

A minor version change is one by which the previous version can be transformed into the next version and back without any loss of teams-js functionality.

## I am making a change to the runtime object. Do I need to update the runtime version?

### *I have added a required property to IRuntime*

- Yes – new minor version is required number if this property can be generated by App SDK without loss of some previously supported functionality, new major version is required if some previously supported functionality is lost when this property is not provided by the host.

### *I have added an optional property or supported capability*

- No new version is required.

### *I have renamed or moved a supported capability*

- Yes – new minor version is required.

### *I have removed a supported capability*

- Yes – new major version is required.

### *I have merged or split capabilities*

- Yes – new major version is required.

## How do I update the runtime version

1. Determine if a major or minor version change is required
2. Update `latestRuntimeApiVersion` in runtime.ts to the new version number
3. Create new interface that extends IBaseRuntime and has apiVersion set to the new version (`IRuntimeVX_Y extends IBaseRuntime { apiVersion: x.y; ... }`)
4. Set type alias Runtime to the new runtime interface (`type Runtime = IRuntimeVX_Y`)
5. Add an `IRuntimeUpgrade` to the `upgradeChain` that will transform the previous runtime version to the new one
6. Update "runtime versioning" tests in `packages/teams-js/test/public/runtime.spec.ts` to use new version number
7. Add a test for upgrading from the previous runtime version to the new one