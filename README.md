# Networked Pool System

The Networked Pool System plugin for Unreal Engine provides a robust and user-friendly solution for object pooling in both offline and networked multiplayer games. By streamlining the reuse of actors and objects, this plugin significantly enhances game performance by minimizing the overhead associated with frequent spawning and destruction.

## Key Features

- **Network Support:** Automatically replicates active states and transforms to clients, removing the need for manual adjustments or modifications by the user.
- **Automatic Variable and Component Reset:** Out-of-the-box resetting of variables and component activation/deactivation ensures minimal setup, allowing users to focus on gameplay rather than technical details.
- **Blueprint and C++ Integration:** Includes K2 nodes and ability tasks for easy spawning and management of pooled objects, making it accessible for both Blueprint and C++ developers.

With the Networked Pool System, you can optimize your multiplayer game’s performance with a simple and intuitive setup process.

## Table of Contents

1. [Pooling Overview](#pooling-overview)
2. [Getting Started](#getting-started)
   - [Setting Up Pooled Actors](#1-setting-up-pooled-actors)
   - [Spawning Pooled Actors](#2-spawning-pooled-actors)
   - [Returning Objects](#3-returning-objects)
3. [Basic Example](#basic-example)
   - [Creating a Pool](#1-creating-a-pool)
   - [Adding Your Pool](#2-adding-your-pool)
   - [Pooled Actor Example](#3-pooled-actor-example)
   - [Checking if an Object is Free](#4-checking-if-an-object-is-free)
4. [Support](#support)

## Pooling Overview

Pooling is a technique used in software development, particularly in gaming, to improve performance by reusing objects or resources instead of creating new ones each time they are needed. Imagine you have a group of identical items, like enemies or projectiles in a game. Instead of creating and destroying these items repeatedly, which can be slow and use up a lot of computer resources, pooling keeps a collection of these items ready to be used and reused.

### Why is Pooling Used?

Pooling is used to make programs run faster and more efficiently. When a game constantly creates and destroys objects, it can slow down because each creation and destruction involves a lot of behind-the-scenes work for the computer. By keeping objects in a pool and reusing them, the game can avoid this costly process, leading to smoother and more responsive gameplay.

### Advantages of Pooling

1. **Improved Performance:** Pooling reduces the time and resources needed to create and destroy objects, making games run faster and more smoothly.
   
2. **Reduced Lag and Stutters:** By reusing objects, pooling helps prevent pauses or stutters in gameplay that can occur when creating or destroying objects.

3. **Efficient Memory Use:** Pooling minimizes memory use by keeping a fixed number of objects that are reused rather than constantly allocating and freeing memory.

Pooling is especially beneficial in high-performance applications like games, where maintaining a seamless and responsive experience is crucial for users.

## Getting Started

### 1. Setting Up Pooled Actors
- The plugin automatically spawns pools on both client and server for handling actors and objects. For expanding on these, refer to [Creating a pool](#1-creating-a-pool).
- Replace the standard `Construct`, `BeginPlay`, and `Destroyed` functions with the corresponding functions from the `UPoolInterface` interface: `OnPoolObjectConstruct`, `OnPoolObjectActivate`, and `OnPoolObjectDeactivate`.

![Setup Example](https://github.com/user-attachments/assets/89514d1e-4abe-48e0-8de3-570cab08b527)

- The system automatically resets trivially copyable variables and arrays containing trivially copyable types. You can exclude variables from this automatic reset by implementing `GetPropertyResetExcludeList`, which returns an FString array list.
Note: If you have a large amount of actors, contantly spawning/despawning, it is recommended to set "Use Autmatic Property Reset" in your project settings to false, since reflection can be heavy if used frequently
![Exclude List Example](https://github.com/user-attachments/assets/6390176f-a2fb-44ac-9f30-1a4a46617ba2)

**Trivially Copyable Variables List:**
- **Basic Data Types:** `bool`, `int8`, `uint8`, `int16`, `uint16`, `int32`, `uint32`, `int64`, `uint64`, `float`, `double`
- **Vectors and Rotators:** `FVector`, `FVector2D`, `FVector4`, `FIntVector`, `FIntPoint`, `FRotator`
- **Math Structures:** `FQuat`, `FTransform`, `FMatrix`
- **Color and Time:** `FColor`, `FLinearColor`, `FTimespan`, `FDateTime`
- **Geometry Structures:** `FBox`, `FBox2D`, `FSphere`, `FPlane`
- **Basic Structs:** `FIntRect`, `FFrameNumber`
- **Enumerations and Bitfields:** Enums (`enum`, `enum class`), Bitfields within structs or classes

### 2. Spawning Pooled Actors
- If spawning pool objects through code, use `UPoolSubsystem::RequestPoolObject`, `UPoolSubsystem::FinishSpawningPoolObject`, and `UPoolSubsystem::ReturnToPool`.

![Code Example](https://github.com/user-attachments/assets/7568e9ea-9729-4ad9-af44-93b0266ffbf4)

- For spawning through Blueprint, K2 nodes and ability tasks are available for use:

![Blueprint Nodes](https://github.com/user-attachments/assets/e3325cb3-b520-45fd-be74-7a4458dbb24e) ![Ability Tasks](https://github.com/user-attachments/assets/e03ef2e3-1881-45cd-ab0c-af2837c70f6f)![image](https://github.com/user-attachments/assets/37bbf586-31f9-414f-b418-4bc1b0a0d118)


### 3. Returning Objects
- Return objects to the pool when they are no longer needed by calling `UPoolSubsystem::ReturnToPool`.

![Return Example](https://github.com/user-attachments/assets/e9e14a2e-58de-49ff-8834-9fb2243cfd37)

- Note: Override `K2_DestroyActor` and `LifeSpanExpired` in your base actor to return to the pool instead of destroying, which allows cleaner usage in Blueprints. `AAExamplePooledActor` in the plugin demonstrates this.

![Blueprint Example](https://github.com/user-attachments/assets/838601f8-5f34-446c-b557-c3f0df807aa3)

## Basic Example

### 1. Creating a Pool
- Create a child class from one of the following classes:

![Pool Classes](https://github.com/user-attachments/assets/a5341d60-6822-42de-bc21-4609e7b62d87)

  - **Base Pool:** Interface for pooling actors with no code, providing a bare-bones pool with property reset.
  - **Actor Pool:** Includes code for setting transform, deactivating and activating components, and enabling/disabling the actor.
  - **Object Pool:** A simple implementation that creates objects if no free objects are available or reuses objects from the pool.

- Example: Creating a new Actor pool based on the base actor pool, "MyBombPool":

![MyBombPool Example](https://github.com/user-attachments/assets/9ff69082-799a-4669-b240-b5cb6718e2ed)

### 2. Adding Your Pool
- Add your pool in the Project Settings tab. Search for "Pool" in the search bar, add your pool to the list, and customize your values, such as actors to spawn at level start, classes to spawn, and whether to spawn only on authority (relevant for multiplayer games).
![image](https://github.com/user-attachments/assets/04f5a9d3-d0fd-4b9c-b58d-25a171b273bf)

### 3. Pooled Actor Example
  - Implementing pooling with Lyra bombs was simple:
  - Reparent the blueprint to your base pooled actor example class.

  ![Reparent Example](https://github.com/user-attachments/assets/376074a3-1eb6-42e8-ac55-8fcac7be4af8)

  - Replace `BeginPlay` and `Destroyed` events with their respective pool `Activate` and `Deactivate` events.

  ![Event Replacement Example](https://github.com/user-attachments/assets/944e2c97-b8e0-46d3-9b52-50ffa596100d)

  - That’s all you need to make bombs pooled!

### 4. Checking if an Object is Free
- Use the `IsActive` function from the subsystem, callable from any Blueprint or C++ class, to check if your actor is currently in use.

![IsActive Example](https://github.com/user-attachments/assets/5e8cbe65-4861-48be-b66d-863004e66ea5)

## Support

For support, requests, questions, or feedback, please contact [joseunrealmarketplace@gmail.com](mailto:joseunrealmarketplace@gmail.com).
