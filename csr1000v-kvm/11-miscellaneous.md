# Miscellaneous 

## Determine the Active vCPU Distribution Template

To determine which template is being used for vCPU distribution, use the following command:
```show platform software cpu alloc```

Example:

```
Router#show platform software cpu alloc
CPU alloc information:
  Control plane cpu alloc: 0
  Data plane cpu alloc: 1
  Service plane cpu alloc: 0
  Template used: None
Router#
```

The Control plane and the Service plane share cores 0.

<br>
