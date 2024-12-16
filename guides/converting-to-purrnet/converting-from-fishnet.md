# Converting from FishNet

{% hint style="info" %}
Keep in mind that both PurrNet and FishNet are systems which are constantly evolving and could be prone to change.
{% endhint %}

Converting from FishNet to PurrNet is likely as simple as it gets. A lot of the naming conventions from FishNet still stands in PurrNet.

It is recommended to do the conversion in a separate project in order to use the "old" project for comparison.

{% hint style="warning" %}
Make sure your project is backed up prior to beginning any conversion!
{% endhint %}

## Converting individual parts (& differences)

### Network Manager

Both **FishNet** and **PurrNet** has a [Network Manager](converting-from-fishnet.md#network-manager) component, which needs to be present for you to start acting on the network. **FishNets** network manager can hold several components like:

* Network Manager
* Transport(s)
* Observer Manager
* Client Manager
* Server Manager
* Time Manager
* Statistics Manager
* Prediction Manager
* etc. etc.

**PurrNet** utilizes internal manager classes for our Network Manager setup, meaning you only generally only need your Network Manager object to hold 2 components:

* Network Manager
* Transport

So setting up your Network Manager with PurrNet is as easy as just making a new gameObject in your scene and adding the Network Manager. See the [Getting Started](../getting-started.md) page for more info.

### NetworkObject vs NetworkIdentity

A major difference between FishNet and PurrNet, is the understanding of ownership and networked objects/identities. In FishNet, NetworkObjects are automatically added to the root of any object that needs to act on the network. Though this adds a solid level of simplicity to the understanding of something like ownership, it can also easily impose some limitations to the developer. \
For example:

❌ Can't nest prefabs that are both network objects, as there can only be one NetworkObject per prefab.\
❌Can't have split ownership on objects\
❌Can't un-parent nested Transforms at runtime

In PurrNet every networked component stands on its own, meaning that every networked component ([Network Identity](../../systems-and-modules/network-identity/)) can have individual ownership, and be nested exactly as you would with the normal Unity workflow.\
For example:

✔️ Can nest prefabs, whether they are networked or not\
✔️ Can have split ownership on a gameobject across several components\
✔️ Can un-parent Transforms as you want at runtime

### Spawning & Despawning

Handling new objects or removing existing networked objects is a bit different between the systems. In FishNet, you'd need to instantiate the object and spawn the attached NetworkObject component. And if you'd want to spawn with ownership, it would be added as a parameter to the spawn call. \
That would look like this:

```csharp
GameObject go = Instantiate(_yourPrefab);
InstanceFinder.ServerManager.Spawn(go, ownerConnection);
```

It's similar for despawning where you have to call the despawn for the NetworkObject component.

In PurrNet, spawning and despawning is handled for you, meaning that if you just want to spawn and object, any networked action handled for that object in the same tick, will arrive with the spawn packet.\
So spawning with ownership using PurrNet would look like this:

```csharp
var identity = Instantiate(myIdentityPrefab);
identity.GiveOwnership(ownerPlayer);
```

And despawning in PurrNet is as easy as just calling Destroy on the object as you would normally in Unity. Give the specific page on [Spawning and despawning](../../systems-and-modules/spawning-and-despawning.md) a read for a more in depth explanation.

### RPC's

Working with RPC's from **FishNet** to **PurrNet** is extremely easy, as the naming conventions are the exact same. However, there are some key differences in terms of usage. This will however depend on your [Network Rules](../../systems-and-modules/network-manager/network-rules.md) so make sure to read up on those. If you are running with a rule set similar to the default "ServerStrict" rules, then the usage will be the same. But if you're running with a rule set similar to the "Unsafe" rules, then you can send any RPC directly from any client!

| FishNet         | PurrNet         |
| --------------- | --------------- |
| \[ServerRpc]    | \[ServerRpc]    |
| \[ObserversRpc] | \[ObserversRpc] |
| \[TargetRpc]    | \[TargetRpc]    |

### Sync types

Working with Synchronizing from FishNet to PurrNet is generally the same as well. The major difference between the two, is that PurrNet also allows for owner authorized SyncTypes, meaning you can handle them locally, and not forcibly through the server. You can read more on his on the individual page for the SyncTypes.\
Our SyncTypes are built using the [Network Modules ](../../systems-and-modules/network-modules.md)of PurrNet, however, usage wise they are nearly identical.

| FishNet        | PurrNet                                                                                   |
| -------------- | ----------------------------------------------------------------------------------------- |
| SyncVar        | [SyncVar](../../systems-and-modules/network-identity/sync-types/syncvar.md)               |
| SyncList       | [SyncList](../../systems-and-modules/network-identity/sync-types/synclist.md)             |
| SyncHashSet    | [SyncHashSet](../../systems-and-modules/network-identity/sync-types/synchashset.md)       |
| SyncDictionary | [SyncDictionary](../../systems-and-modules/network-identity/sync-types/syncdictionary.md) |
| SyncTimer      | [SyncTimer](../../systems-and-modules/network-identity/sync-types/synctimer.md)           |

There are few functional differences with some of them, for example the SyncTimer of PurrNet handles reconciliation, ensuring that the timers align on clients and doesn't get de-synced

### Callbacks

Callbacks in FishNet is found on the respective manager component which the NetworkManager works with.\
Using PurrNet, all the callbacks are found in the [NetworkManager ](../../systems-and-modules/network-manager/)itself. The video on the [NetworkManager](../../systems-and-modules/network-manager/) found on the relative page, explains this more in depth. Using your IDE should give you access to all the callbacks you need.