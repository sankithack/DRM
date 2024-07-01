# DRM

# `exynos_drm_drv.c` Detailed Explanation

## File Overview

`exynos_drm_drv.c` is a driver source file for the Exynos DRM (Direct Rendering Manager) in the Linux kernel. This file includes initialization, binding, unbinding, load, unload, suspend, and resume functionalities for the DRM device.

## Code Breakdown

### Includes and Function Prototypes

```c
#include <drm/drmP.h>
#include <drm/drm_crtc_helper.h>
#include <drm/drm_fb_helper.h>
#include <linux/console.h>
#include <linux/pm_runtime.h>
#include "exynos_drm_drv.h"
#include "exynos_drm_fb.h"
#include "exynos_drm_crtc.h"
```

- **Includes**: Necessary headers for DRM, Linux kernel operations, and Exynos-specific DRM functionalities.

### Function Prototypes

```c
static int exynos_drm_bind(struct device *dev);
static void exynos_drm_unbind(struct device *dev);
static int exynos_drm_load(struct drm_device *dev, unsigned long flags);
static int exynos_drm_unload(struct drm_device *dev);
static int exynos_drm_open(struct drm_device *dev, struct drm_file *file);
static void exynos_drm_release(struct drm_device *dev, struct drm_file *file);
```

- **Prototypes**: Declare functions for binding, unbinding, loading, unloading, opening, and releasing the DRM device.

### File Operations

```c
static const struct file_operations exynos_drm_fops = {
    .owner = THIS_MODULE,
    .open = drm_open,
    .release = drm_release,
    .unlocked_ioctl = drm_ioctl,
    .poll = drm_poll,
    .read = drm_read,
    .llseek = no_llseek,
};
```

- **exynos_drm_fops**: Defines file operations for the DRM driver.

### DRM Driver Structure

```c
static const struct drm_driver exynos_drm_driver = {
    .driver_features = DRIVER_GEM | DRIVER_MODESET | DRIVER_ATOMIC,
    .fops = &exynos_drm_fops,
    .name = "exynos",
    .desc = "Exynos DRM",
    .date = "20120210",
    .major = 1,
    .minor = 0,
    .patchlevel = 0,
    .load = exynos_drm_load,
    .unload = exynos_drm_unload,
    .open = exynos_drm_open,
    .release = exynos_drm_release,
    .ioctls = exynos_ioctls,
    .num_ioctls = ARRAY_SIZE(exynos_ioctls),
};
```

- **exynos_drm_driver**: Defines the main DRM driver structure, specifying features, file operations, and callbacks.

### Function Implementations

#### `exynos_drm_bind`

```c
static int exynos_drm_bind(struct device *dev)
{
    struct drm_device *drm_dev;
    int ret;

    drm_dev = drm_dev_alloc(&exynos_drm_driver, dev);
    if (IS_ERR(drm_dev))
        return PTR_ERR(drm_dev);

    ret = drm_dev_register(drm_dev, 0);
    if (ret)
        drm_dev_put(drm_dev);

    return ret;
}
```

- **drm_dev_alloc**: Allocates and initializes a DRM device.
- **drm_dev_register**: Registers the DRM device.
- **drm_dev_put**: Releases the device if registration fails.

#### `exynos_drm_unbind`

```c
static void exynos_drm_unbind(struct device *dev)
{
    struct drm_device *drm_dev = dev_get_drvdata(dev);

    drm_dev_unregister(drm_dev);
    drm_dev_put(drm_dev);
}
```

- **drm_dev_unregister**: Unregisters the DRM device.
- **drm_dev_put**: Releases the device.

#### `exynos_drm_load`

```c
static int exynos_drm_load(struct drm_device *dev, unsigned long flags)
{
    int ret;

    ret = exynos_drm_mode_config_init(dev);
    if (ret)
        return ret;

    drm_mode_config_reset(dev);
    drm_kms_helper_poll_init(dev);

    return 0;
}
```

- **exynos_drm_mode_config_init**: Initializes mode configuration.
- **drm_mode_config_reset**: Resets mode configuration.
- **drm_kms_helper_poll_init**: Initializes KMS helper polling.

#### `exynos_drm_unload`

```c
static int exynos_drm_unload(struct drm_device *dev)
{
    drm_kms_helper_poll_fini(dev);
    exynos_drm_mode_config_fini(dev);

    return 0;
}
```

- **drm_kms_helper_poll_fini**: Cleans up KMS helper polling.
- **exynos_drm_mode_config_fini**: Finalizes mode configuration.

#### `exynos_drm_open`

```c
static int exynos_drm_open(struct drm_device *dev, struct drm_file *file)
{
    return drm_open_helper(file, dev);
}
```

- **drm_open_helper**: Handles opening the DRM device file.

#### `exynos_drm_release`

```c
static void exynos_drm_release(struct drm_device *dev, struct drm_file *file)
{
    drm_release(dev, file);
}
```

- **drm_release**: Handles releasing the DRM device file.

#### `exynos_drm_suspend`

```c
static int exynos_drm_suspend(struct device *dev)
{
    struct drm_device *drm_dev = dev_get_drvdata(dev);
    return drm_mode_config_helper_suspend(drm_dev);
}
```

- **drm_mode_config_helper_suspend**: Suspends the mode configuration helper.

#### `exynos_drm_resume`

```c
static int exynos_drm_resume(struct device *dev)
{
    struct drm_device *drm_dev = dev_get_drvdata(dev);
    return drm_mode_config_helper_resume(drm_dev);
}
```

- **drm_mode_config_helper_resume**: Resumes the mode configuration helper.

### Power Management Operations

```c
static const struct dev_pm_ops exynos_drm_pm_ops = {
    .suspend = exynos_drm_suspend,
    .resume = exynos_drm_resume,
};
```

- **exynos_drm_pm_ops**: Defines power management operations.

### Platform Driver

```c
static struct platform_driver exynos_drm_platform_driver = {
    .probe = exynos_drm_probe,
    .remove = exynos_drm_remove,
    .driver = {
        .name = "exynos-drm",
        .pm = &exynos_drm_pm_ops,
    },
};
```

- **exynos_drm_platform_driver**: Defines the platform driver.

### Module Initialization and Exit

```c
static int __init exynos_drm_init(void)
{
    return platform_driver_register(&exynos_drm_platform_driver);
}

static void __exit exynos_drm_exit(void)
{
    platform_driver_unregister(&exynos_drm_platform_driver);
}

module_init(exynos_drm_init);
module_exit(exynos_drm_exit);

MODULE_AUTHOR("Samsung Electronics");
MODULE_DESCRIPTION("Samsung Exynos DRM Driver");
MODULE_LICENSE("GPL");
```

- **exynos_drm_init**: Registers the platform driver during module initialization.
- **exynos_drm_exit**: Unregisters the platform driver during module exit.

---

This detailed explanation covers the primary functions and their roles within the `exynos_drm_drv.c` file, providing a comprehensive understanding of how the Exynos DRM driver operates within the Linux kernel.
