<!--
---
linkTitle: "Migrating from Tekton v1beta1"
weight: 4000
---
-->

# Migrating From Tekton `v1beta1` to Tekton `v1`

- [Changes to fields](#changes-to-fields)
- [Upgrading `PipelineRun.Timeout` to `PipelineRun.Timeouts`](#upgrading-pipelinerun.timeout-to-pipelinerun.timeouts)
- [Deprecating Resources from Task, TaskRun, Pipeline and PipelineRun](#deprecating-resources-from-task,-taskrun,-pipeline-and-pipelinerun)
- [Replacing `taskRef.bundle` and `pipelineRef.bundle` with Bundle Resolver](#replacing-taskRef.bundle-and-pipelineRef.bundle-with-bundle-resolver)


This document describes the differences between `v1beta1` Tekton entities and their
`v1` counterparts. It also describes the changed fields and the deprecated fields into v1.
## Changes to fields

In Tekton `v1`, the following fields have been changed:

| Old field | Replacement |
| --------- | ----------|
| `pipelineRun.spec.Timeout`| `pipelineRun.spec.timeouts.pipeline` |
| `pipelineRun.spec.taskRunSpecs.taskServiceAccountName` | `pipelineRun.spec.taskRunSpecs.serviceAccountName` |
| `pipelineRun.spec.taskRunSpecs.taskPodTemplate` | `pipelineRun.spec.taskRunSpecs.podTemplate` |
| `taskRun.status.taskResults` | `taskRun.status.results` |
| `pipelineRun.status.pipelineResults` | `pipelineRun.status.results` |
| `taskRun.spec.taskRef.bundle` | `taskRun.spec.taskRef.resolver` |
| `pipelineRun.spec.pipelineRef.bundle` | `pipelineRun.spec.pipelineRef.resolver` |
| `task.spec.resources` | removed from `Task` |
| `taskrun.spec.resources` | removed from `TaskRun` |
| `pipeline.spec.resources` | removed from `Pipeline` |
| `pipelineRun.spec.resources` | removed from `PipelineRun` |

## Deprecating `resources` from Task, TaskRun, Pipeline and PipelineRun
`PipelineResources` are deprecated, and the `resources` fields of Task, TaskRun, Pipeline and PipelineRun has been removed. Please use `Tasks` instead. For more information, see [Replacing PipelineResources](https://github.com/tektoncd/pipeline/blob/main/docs/pipelineresources.md)

## Replacing `taskRef.bundle` and `pipelineRef.bundle` with Bundle Resolver
Bundle resolver in remote resolution should be used instead of `taskRun.spec.taskRef.bundle` and `pipelineRun.spec.pipelineRef.bundle`.

The [`enable-bundles-resolver`](https://github.com/tektoncd/pipeline/blob/main/docs/install.md#customizing-the-pipelines-controller-behavior) feature flag must be enabled to use this feature.

```yaml
apiVersion: tekton.dev/v1beta1
kind: Taskrun
spec:
  taskRef:
    name: example-task
    bundle: python:3-alpine
---
apiVersion: tekton.dev/v1beta1
kind: Taskrun
spec:
  taskRef:
    resolver: bundles
    params:
    - name: bundle
      value: python:3-alpine
    - name: name
      value: taskName
    - name: kind
      value: Task
```

## Replacing ClusterTask with Remote Resolution
`ClusterTask` is deprecated. Please use the `cluster` resolver instead.

The [`enable-cluster-resolver`](https://github.com/tektoncd/pipeline/blob/main/docs/install.md#customizing-the-pipelines-controller-behavior) feature flag must be enabled to use this feature.

The `cluster` resolver allows `Pipeline`s, `PipelineRun`s, and `TaskRun`s to refer
to `Pipeline`s and `Task`s defined in other namespaces in the cluster.

```yaml
apiVersion: tekton.dev/v1beta1
kind: TaskRun
metadata:
  name: cluster-task-reference
spec:
  taskRef:
    resolver: cluster
    params:
    - name: kind
      value: task
    - name: name
      value: example-task
    - name: namespace
      value: example-namespace
```

For more information, see [Remote resolution](https://github.com/tektoncd/community/blob/main/teps/0060-remote-resource-resolution.md).
