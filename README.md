# LWJGL OpenGL AMD Optifine crash fix
#### It prevents your game to crash while enabling shaders like SEUS, Chocapic13, etc.

Optifine crash due to glShaderSourceARB() function fails on some AMD drivers while enabling shaders

---

The crash has been solved by [BalintCsal](https://github.com/BalintCsala) here: [AMD crash on recent drivers with provided workaround](https://github.com/sp614x/optifine/issues/5852)

This project provides you only with the source code of the modified LWJGL-OpenGL (3.2.2) [org.lwjgl.opengl.ARBShaderObjects.java](https://github.com/gamerover98/Optifine-AMD-fix/blob/main/src/main/java/org/lwjgl/opengl/ARBShaderObjects.java) class.

## Steps to reprodice

- Install Optifine on a system with an AMD graphics card using the latest drivers.
- Install a shaderpack and try to enable it.

## Installation and fix
#### [MultiMC](https://multimc.org/#Download) is required!

- Follow the BaliantCsal [PDF guide](https://github.com/sp614x/optifine/files/6702261/Getting_Optifine_shaders_working_on_some_AMD_cards.1.pdf)
- Copy the MultiMC LWJGL json file from [here](https://pastebin.com/raw/Lx8KGgie)

![2021-07-10_12 28 52](https://user-images.githubusercontent.com/9408687/125159907-8515f300-e17a-11eb-81a4-5560672540b9.png)

---

### How does it work
This simple Maven project provides you a copy of the original ARBShaderObjects class from the OpenGL module of LWJGL 3.2.2 (current Minecraft LWJGL version).

The [ARBShaderObjects.glShaderSourceARB(shaderObj, string)](https://github.com/gamerover98/Optifine-AMD-fix/blob/5ff71ff63275d5d14fe08c0e4d9fa477b0a12b5c/src/main/java/org/lwjgl/opengl/ARBShaderObjects.java#L213) method has been replaced with the following code:

```java
MemoryStack stack = stackGet();
int stackPointer = stack.getPointer();

try {

    ByteBuffer sourceBuffer = MemoryUtil.memUTF8(string, true);
    PointerBuffer pointers = stack.mallocPointer(1);
    pointers.put(sourceBuffer);

    long stringAddress = org.lwjgl.system.APIUtil.apiArrayi(stack, MemoryUtil::memUTF8, string);
    nglShaderSourceARB(shaderObj, 1, pointers.address0(), 0);
    org.lwjgl.system.APIUtil.apiArrayFree(stringAddress, 1);

} finally {
    stack.setPointer(stackPointer);
}
```

### How to patch the LWJGL-OpenGL jar file
#### [Apache Maven software](https://maven.apache.org/download.cgi) is required to compile this project


- Download this repository as zip or clone it
- Type: `mvn clean compile`
- After compiling, you'll find the compiled ARBShaderObjects class at target\classes\org\lwjgl\opengl
- Download LWJGL OpenGL 3.2.2 Jar from the [manve repo](https://mvnrepository.com/artifact/org.lwjgl/lwjgl-opengl/3.2.2)
- Open it with 7Zip, WinRar or similar
- Replace the org\lwjgl\opengl\ARBShaderObjects.class zip file with your.

### Disclaimer
I'm not the author of this fix. 
Waiting for an official Optifine update, I provide you with an easy way to use shaders with your AMD GPU.
