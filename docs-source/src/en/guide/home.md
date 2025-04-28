![Screenshot_2025-03-18-03-21-49-32_b7e9024f54bdc0532418cb6e23c85a7c](https://github.com/user-attachments/assets/39afdbbb-8833-41de-b930-746a997e1ed2)
![Screenshot_2025-04-24-10-33-02-57_75eb11136b7a599084dfb72f722b6ef4](https://github.com/user-attachments/assets/fda9b53c-9b48-4252-bc29-c1d133b0e69b)
![Screenshot_2025-04-24-10-33-02-57_75eb11136b7a599084dfb72f722b6ef4](https://github.com/user-attachments/assets/a3c12793-0309-41c3-8ffc-81624d5173c4)
![17458037047912577950981859007771](https://github.com/user-attachments/assets/0e5531e2-713c-44f6-afba-f8c2111e27e3)
# Introduce

> `YukiHookAPI` is an integrated Hook API Framework, which does not provide any Hook functions, and needs the support of Xposed related basic APIs.

## Background

This is an efficient Hook API rebuilt based on the Xposed API using Kotlin, and creates rich function extensions for the development of Xposed Modules.

The name is taken from ["ももくり" heroine Yuki Kurihara](https://www.bilibili.com/bangumi/play/ss5016).

Formerly the Innocent Xposed API used in [Development Learning Project](https://github.com/fankes/TMore), now renamed and open sourced.

## Usage

`YukiHookAPI` is built entirely with Kotlin **lambda** syntax.

Abandoning the original less friendly `XposedHelpers`, you can use it to easily create Xposed Modules and easily implement custom Hook API.

## Language Requirement

Please use Kotlin, the framework part of the code composition is also compatible with Java but the implementation of the basic Hook scene **may not work at all**.

All demo code in this document will be described using Kotlin, if you don't know how to use Kotlin then you may not be able to use `YukiHookAPI`.

Part of the Java Demo code can be found [here](https://github.com/HighCapable/YukiHookAPI/tree/master/samples/demo-module/src/main/java/com/highcapable/yukihookapi/demo_module/hook/java), but not recommended.

## Source of Inspiration

Previously, when we built an Xposed Module, we first needed to create an `xposed_init` file under `assets`.

Then, manually fill in your own entry class name into the file and use `XposedHelpers` to implement our Hook logic.

Since Kotlin is the main Android development language, this API is really not very elegant to use.

Is there any **easy to use, light, elegant** solution?

With this idea, `YukiHookAPI` was born.

Now, we only need to write a small amount of code, and all the time and expense are handed over to automation.

With Kotlin's elegant **lambda** writing and `YukiHookAPI`, you can make your Hook logic more beautiful and clear.

> The following example

:::: code-group
::: code-group-item Yuki Hook API

```kotlin
@InjectYukiHookWithXposed
object HookEntry : IYukiHookXposedInit {

    override fun onHook() = encase {
        loadZygote {
            ActivityClass.method {
                name = "onCreate"
                param(BundleClass)
            }.hook {
                before {
                  // Your code here.
                }
                after {
                  // Your code here.
                }
            }
        }
        loadApp(name = "com.android.browser") {
            ActivityClass.method {
                name = "onCreate"
                param(BundleClass)
            }.hook {
                before {
                  // Your code here.
                }
                after {
                  // Your code here.
                }
            }
        }
    }
}
```

:::
::: code-group-item Rovo89 Xposed API

```kotlin
class HookEntry : IXposedHookZygoteInit, IXposedHookLoadPackage {

    private lateinit var moduleResources: XModuleResources

    override fun initZygote(sparam: IXposedHookZygoteInit.StartupParam) {
        moduleResources = XModuleResources.createInstance(sparam.modulePath, null)
        XposedHelpers.findAndHookMethod(
                Activity::class.java.name,
                null, "onCreate",
                Bundle::class.java,
                object : XC_MethodHook() {
                    override fun beforeHookedMethod(param: MethodHookParam?) {
                        // Your code here.
                    }

                    override fun afterHookedMethod(param: MethodHookParam?) {
                        // Your code here.
                    }
                })
    }

    override fun handleLoadPackage(lpparam: XC_LoadPackage.LoadPackageParam) {
        if (lpparam.packageName == "com.android.browser")
            XposedHelpers.findAndHookMethod(
                Activity::class.java.name,
                lpparam.classLoader, "onCreate",
                Bundle::class.java,
                object : XC_MethodHook() {
                    override fun beforeHookedMethod(param: MethodHookParam?) {
                        // Your code here.
                    }

                    override fun afterHookedMethod(param: MethodHookParam?) {
                        // Your code here.
                    }
                })
    }
}
```

:::
::::

Yes, you read that right, just needing these codes can completely replace the traditional Xposed API to achieve the same function.

Now, with the help of the efficient and powerful `Yuk![logo](https://github.com/user-attachments/assets/5a78affd-bc56-4c4a-8662-353939188359)
iHookAPI`, you can implement a very simple Xposed Module.
