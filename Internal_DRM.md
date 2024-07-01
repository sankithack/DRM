# `exynos_drm_drv.c` Detailed Explanation with Inner Function Calls

## Function Implementations with Inner Function Calls

### `exynos_drm_bind`

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

#### Inner Functions:
1. **`drm_dev_alloc`**: Allocates and initializes a DRM device structure.
   ```c
   struct drm_device *drm_dev;
   drm_dev = drm_dev_alloc(&exynos_drm_driver, dev);
   ```

2. **`IS_ERR`**: Checks if the pointer is an error pointer.
   ```c
   if (IS_ERR(drm_dev))
   ```

3. **`PTR_ERR`**: Returns the error code from an error pointer.
   ```c
   return PTR_ERR(drm_dev);
   ```

4. **`drm_dev_register`**: Registers the DRM device with the DRM core.
   ```c
   ret = drm_dev_register(drm_dev, 0);
   ```

5. **`drm_dev_put`**: Decreases the reference count of the DRM device and releases it if the count reaches zero.
   ```c
   if (ret)
       drm_dev_put(drm_dev);
   ```

### `exynos_drm_unbind`

```c
static void exynos_drm_unbind(struct device *dev)
{
    struct drm_device *drm_dev = dev_get_drvdata(dev);

    drm_dev_unregister(drm_dev);
    drm_dev_put(drm_dev);
}
```

#### Inner Functions:
1. **`dev_get_drvdata`**: Retrieves the driver data associated with a device.
   ```c
   struct drm_device *drm_dev = dev_get_drvdata(dev);
   ```

2. **`drm_dev_unregister`**: Unregisters the DRM device from the DRM core.
   ```c
   drm_dev_unregister(drm_dev);
   ```

3. **`drm_dev_put`**: Decreases the reference count of the DRM device and releases it if the count reaches zero.
   ```c
   drm_dev_put(drm_dev);
   ```

### `exynos_drm_load`

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

#### Inner Functions:
1. **`exynos_drm_mode_config_init`**: Initializes the mode configuration for the Exynos DRM device.
   ```c
   ret = exynos_drm_mode_config_init(dev);
   ```

2. **`drm_mode_config_reset`**: Resets the DRM mode configuration.
   ```c
   drm_mode_config_reset(dev);
   ```

3. **`drm_kms_helper_poll_init`**: Initializes polling for KMS (Kernel Mode Setting) helper functions.
   ```c
   drm_kms_helper_poll_init(dev);
   ```

### `exynos_drm_unload`

```c
static int exynos_drm_unload(struct drm_device *dev)
{
    drm_kms_helper_poll_fini(dev);
    exynos_drm_mode_config_fini(dev);

    return 0;
}
```

#### Inner Functions:
1. **`drm_kms_helper_poll_fini`**: Cleans up polling for KMS helper functions.
   ```c
   drm_kms_helper_poll_fini(dev);
   ```

2. **`exynos_drm_mode_config_fini`**: Finalizes the mode configuration for the Exynos DRM device.
   ```c
   exynos_drm_mode_config_fini(dev);
   ```

### `exynos_drm_open`

```c
static int exynos_drm_open(struct drm_device *dev, struct drm_file *file)
{
    return drm_open_helper(file, dev);
}
```

#### Inner Functions:
1. **`drm_open_helper`**: Opens a DRM file and associates it with the DRM device.
   ```c
   return drm_open_helper(file, dev);
   ```

### `exynos_drm_release`

```c
static void exynos_drm_release(struct drm_device *dev, struct drm_file *file)
{
    drm_release(dev, file);
}
```

#### Inner Functions:
1. **`drm_release`**: Releases a DRM file and disassociates it from the DRM device.
   ```c
   drm_release(dev, file);
   ```

### `exynos_drm_suspend`

```c
static int exynos_drm_suspend(struct device *dev)
{
    struct drm_device *drm_dev = dev_get_drvdata(dev);
    return drm_mode_config_helper_suspend(drm_dev);
}
```

#### Inner Functions:
1. **`dev_get_drvdata`**: Retrieves the driver data associated with a device.
   ```c
   struct drm_device *drm_dev = dev_get_drvdata(dev);
   ```

2. **`drm_mode_config_helper_suspend`**: Suspends the DRM mode configuration.
   ```c
   return drm_mode_config_helper_suspend(drm_dev);
   ```

### `exynos_drm_resume`

```c
static int exynos_drm_resume(struct device *dev)
{
    struct drm_device *drm_dev = dev_get_drvdata(dev);
    return drm_mode_config_helper_resume(drm_dev);
}
```

#### Inner Functions:
1. **`dev_get_drvdata`**: Retrieves the driver data associated with a device.
   ```c
   struct drm_device *drm_dev = dev_get_drvdata(dev);
   ```

2. **`drm_mode_config_helper_resume`**: Resumes the DRM mode configuration.
   ```c
   return drm_mode_config_helper_resume(drm_dev);
   ```

### Full Code with Inner Function Calls Explained

```c
#include <drm/drmP.h>
#include <drm/drm_crtc_helper.h>
#include <drm/drm_fb_helper.h>
#include <linux/console.h>
#include <linux/pm_runtime.h>
#include "exynos_drm_drv.h"
#include "exynos_drm_fb.h"
#include "exynos_drm_crtc.h"

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

static void exynos_drm_unbind(struct device *dev)
{
    struct drm_device *drm_dev = dev_get_drvdata(dev);

    drm_dev_unregister(drm_dev);
    drm_dev_put(drm_dev);
}

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

static int exynos_drm_unload(struct drm_device *dev)
{
    drm_kms_helper_poll_fini(dev);
    exynos_drm_mode_config_fini(dev);

    return 0;
}

static int exynos_drm_open(struct drm_device *dev, struct drm_file *file)
{
    return drm_open_helper(file, dev);
}

static void exynos_drm_release(struct drm_device *dev, struct drm_file *file)
{
    drm_release(dev, file);
}

static int exynos_drm_suspend(struct device *dev)
{
    struct drm_device *drm_dev = dev_get_drvdata(dev);
    return drm_mode_config_helper_suspend(drm_dev);
}

static int exynos_drm_resume(struct device *dev)
{
    struct drm_device *drm_dev = dev_get_drvdata(dev);
    return drm_mode_config_helper_resume(drm_dev);
}

static const struct dev_pm_ops exynos_drm_pm_ops = {
    .suspend = exynos_drm_suspend,
    .resume = exynos_drm_resume,
};

static struct platform_driver exynos_drm_platform_driver = {
    .probe = exynos_drm_probe,
    .remove = exynos_drm_remove,
    .driver = {
        .name = "exynos-drm",
        .pm = &exynos_drm_pm_ops,
    },
};

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

This detailed explanation provides an overview of each function and the inner functions they call, along with their roles within the `exynos_drm_drv.c` file.
