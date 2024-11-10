# Tarsave - Save and Load System

# Table Of Contents
* [Introduction](#introduction)
* [Installation](#installation)
* [How to Use](#how-to-use)
    * [Encryption](#encryption)
    * [SaveManager](#savemanager)
    * [TransformSaveManager](#transformsavemanager)
    * [AudioSaveManager](#audiosavemanager)

# Introduction
Tarsave is an encrypted, highly optimized and easy-to-use save and load system developed for Unity.

# Installation
You can download the package from [Unity Asset Store](https://assetstore.unity.com/packages/slug/301159)

# How to Use
After importing this package into your project, you can read this document to get information about how to use it and examine the `Samples` folder for sample uses.

## Encryption
`AES` (Advanced Encryption Standard) is an encryption algorithm used to securely protect data. It uses a specific key to encrypt the data, making it impossible to understand the encrypted content without this key. The encryption process hides the original data using complex mathematical transformations to ensure its security. The same key is also used to decrypt the data, allowing the original content to be retrieved quickly and reliably, ensuring both efficiency and strong protection.
In Unity's `Tools/Tarsave/Settings` menu, developers can generate Key and IV values for this encryption according to the key size.

## SaveManager
Data that does not contain Unity classes are saved via this manager. This SaveManager class simplifies saving, loading, and deleting data with support for both synchronous and asynchronous operations, and also provides auto-saving functionality.

1. `Save<T>(string key, T data) where T : class`
`Purpose`: Saves data of type T to a specified file path.
`Parameters`:
key: A string that is used to determine the file path.
data: The data to be saved, constrained to reference types (classes).
`Return Value`: Returns true if the save operation was successful, false otherwise.

2. `SaveAsync<T>(string key, T data, CancellationToken cancellationToken = default) where T : class`
`Purpose`: Saves data of type T asynchronously to a specified file path.
`Parameters`:
key: A string that specifies the file path.
data: The data to save, constrained to reference types (classes).
cancellationToken: A token to signal cancellation of the save operation.
`Return Value`: Returns a Task<bool> that indicates whether the save operation was successful.

3. `AutoSaveAsync<T>(string key, T data, int intervalSeconds, CancellationToken cancellationToken) where T : class`
`Purpose`: Periodically saves data of type T asynchronously at regular intervals.
`Parameters`:
key: A string to define the file path.
data: The data to save periodically, constrained to reference types.
intervalSeconds: The time interval (in seconds) between each save operation.
cancellationToken: A token to cancel the auto-save loop.
`Return Value`: An async Task.

4. `Load<T>(string key) where T : class`
`Purpose`: Loads data of type T from a specified file path.
`Parameters`:
key: A string used to determine the file path for loading the data.
`Return Value`: Returns the loaded data of type T or null if loading fails.

5. `LoadAsync<T>(string key, CancellationToken cancellationToken = default) where T : class`
`Purpose`: Loads data of type T asynchronously from a specified file path.
`Parameters`:
key: A string for the file path.
cancellationToken: A token to signal cancellation of the load operation.
`Return Value`: Returns a Task<T> that contains the loaded data of type T or null if loading fails.

6. `Delete(string key)`
`Purpose`: Deletes a file corresponding to the specified key.
`Parameters`:
key: A string that specifies the file path to be deleted.
`Return Value`: Returns true if the file deletion was successful, false otherwise.

7. `DeleteAll()`
`Purpose`: Deletes all files in the configured save directory.
`Parameters`: None.
`Return Value`: Returns true if the deletion of the directory was successful, false otherwise.

```csharp
public class SaveManagerSampleObject : MonoBehaviour
{
    private const string DATA_KEY = "Tarsave/Samples/SaveManagerSampleObject";

    [SerializeField]
    private PlayerData _playerData;

    public void Save()
    {
        bool result = SaveManager.Save(DATA_KEY, _playerData);
    }

    public async Task SaveAsync()
    {
        bool result = await SaveManager.SaveAsync(DATA_KEY, _playerData, gameObject.GetCancellationTokenOnDestroy());
    }

    public void AutoSave()
    {
        SaveManager.AutoSaveAsync(DATA_KEY, _playerData, 3, gameObject.GetCancellationTokenOnDestroy()).Forget();
    }

    public void Load()
    {
        _playerData = SaveManager.Load<PlayerData>(DATA_KEY);
    }

    public async Task LoadAsync()
    {
        _playerData = await SaveManager.LoadAsync<PlayerData>(DATA_KEY, gameObject.GetCancellationTokenOnDestroy());
    }

    public void Delete()
    {
        bool result = SaveManager.Delete(DATA_KEY);
    }

    public void DeleteAll()
    {
        bool result = SaveManager.DeleteAll();
    }
}
```

## TransformSaveManager
Data containing the Unity Transform class is saved via this manager. This TransformSaveManager class simplifies saving, loading, and deleting data with support for both synchronous and asynchronous operations, and also provides auto-saving functionality.

1. `Save(string key, Transform transform)`
`Purpose`: Saves a single Transform object.
`Parameters`:
key: A string used to generate the save file path.
transform: The Transform object to save.
`Return Value`: Returns true if the save operation was successful, false otherwise.

2. `Save(string key, Transform[] transforms)`
`Purpose`: Saves an array of Transform objects.
`Parameters`:
key: A string used to generate the save file path.
transforms: An array of Transform objects to save.
`Return Value`: Returns true if the save operation was successful, false otherwise.

3. `SaveAsync(string key, Transform transform, CancellationToken cancellationToken = default)`
`Purpose`: Asynchronously saves a single Transform object.
`Parameters`:
key: A string used to generate the save file path.
transform: The Transform object to save.
cancellationToken: A token to signal cancellation.
`Return Value`: Returns a Task<bool> that indicates whether the save operation was successful.

4. `SaveAsync(string key, Transform[] transforms, CancellationToken cancellationToken = default)`
`Purpose`: Asynchronously saves an array of Transform objects.
`Parameters`:
key: A string used to generate the save file path.
transforms: An array of Transform objects to save.
cancellationToken: A token to signal cancellation.
`Return Value`: Returns a Task<bool> indicating if the save was successful.

5. `AutoSaveAsync(string key, Transform transform, int intervalSeconds, CancellationToken cancellationToken)`
`Purpose`: Periodically saves a single Transform object asynchronously at specified intervals.
`Parameters`:
key: A string used for the save path.
transform: The Transform to save periodically.
intervalSeconds: The interval between saves in seconds.
cancellationToken: A token to signal cancellation.
`Return Value`: An async Task.

6. `AutoSaveAsync(string key, Transform[] transforms, int intervalSeconds, CancellationToken cancellationToken)`
`Purpose`: Periodically saves an array of Transform objects asynchronously at set intervals.
`Parameters`:
key: A string for the save path.
transforms: An array of Transform objects to save periodically.
intervalSeconds: The interval between saves in seconds.
cancellationToken: A token to signal cancellation.
`Return Value`: An async Task.

7. `Load(string key, Transform transform)`
`Purpose`: Loads saved data and applies it to a Transform object.
`Parameters`:
key: A string used to determine the file path for loading.
transform: The Transform object to apply the loaded data to.
`Return Value`: None.

8. `Load(string key, Transform[] transforms)`
`Purpose`: Loads an array of saved TransformData and applies them to an array of Transform objects.
`Parameters`:
key: A string used to generate the load path.
transforms: An array of Transform objects to apply the loaded data to.
`Return Value`: None.

9. `LoadAsync(string key, Transform transform, CancellationToken cancellationToken = default)`
`Purpose`: Asynchronously loads data and applies it to a Transform object.
`Parameters`:
key: A string used to generate the load path.
transform: The Transform object to apply the loaded data to.
cancellationToken: A token to signal cancellation.
`Return Value`: An async Task.

10. `LoadAsync(string key, Transform[] transforms, CancellationToken cancellationToken = default)`
`Purpose`: Asynchronously loads an array of saved TransformData and applies them to an array of Transform objects.
`Parameters`:
key: A string for the load path.
transforms: An array of Transform objects to apply the loaded data to.
cancellationToken: A token to signal cancellation.
`Return Value`: An async Task.

```csharp
public class TransformSaveManagerSampleObject : MonoBehaviour
{
    private const string DATA_KEY = "Tarsave/Samples/TransformSaveManagerSampleObject";
    
    [SerializeField]
    private Transform[] _transforms;

    public void Save()
    {
        bool result = TransformSaveManager.Save(DATA_KEY, _transforms);
    }

    public async Task SaveAsync()
    {
        bool result = await TransformSaveManager.SaveAsync(DATA_KEY, _transforms, gameObject.GetCancellationTokenOnDestroy());
    }

    public void AutoSave()
    {
        TransformSaveManager.AutoSaveAsync(DATA_KEY, _transforms, 3, gameObject.GetCancellationTokenOnDestroy()).Forget();
    }

    public void Load()
    {
        TransformSaveManager.Load(DATA_KEY, _transforms);
    }

    public async Task LoadAsync()
    {
        await TransformSaveManager.LoadAsync(DATA_KEY, _transforms, gameObject.GetCancellationTokenOnDestroy());
    }
}
```

## AudioSaveManager
Data containing the Unity AudioClip class is saved via this manager. This AudioSaveManager class simplifies saving, loading, and deleting data with support for both synchronous and asynchronous operations, and also provides auto-saving functionality.
Remember that you can save audio files with up to 10 million samples for optimization! You can try splitting the audio files for more saving.

1. `Save(string key, AudioClip audioClip)`
`Purpose`: Saves an AudioClip object synchronously to storage.
`Parameters`:
key: A unique string identifier used to create the file path where the AudioClip will be saved.
audioClip: The AudioClip object to be saved.
`Return Value`: Returns true if the AudioClip is successfully saved, otherwise returns false.

2. `SaveAsync(string key, AudioClip audioClip, CancellationToken cancellationToken = default)`
`Purpose`: Saves an AudioClip asynchronously to storage.
`Parameters`:
key: A unique string identifier for the save path.
audioClip: The AudioClip object to be saved.
cancellationToken: Optional token to cancel the asynchronous operation.
`Return Value`: Returns a Task<bool> that indicates whether the save operation was successful (true) or failed (false).

3. `Load(string key)`
`Purpose`: Loads a saved AudioClip from storage.
`Parameters`:
key: The unique identifier used to locate the saved AudioClip.
`Return Value`: Returns the loaded AudioClip or null if the load operation failed (e.g., if the data is empty or missing).

4. `LoadAsync(string key, CancellationToken cancellationToken = default)`
`Purpose`: Asynchronously loads a saved AudioClip from storage.
`Parameters`:
key: The unique identifier used to locate the saved AudioClip.
cancellationToken: Optional token to cancel the asynchronous operation.
`Return Value`: Returns a Task<AudioClip> that resolves to the loaded AudioClip or null if the load operation failed.

```csharp
public class AudioSaveManagerSampleObject : MonoBehaviour
{
    private const string DATA_KEY = "Tarsave/Samples/AudioSaveManagerSampleObject";
    
    [SerializeField]
    private AudioSource _audioSource;

    public void Save()
    {
        bool result = AudioSaveManager.Save(DATA_KEY, _audioSource.clip);
    }

    public async Task SaveAsync()
    {
        bool result = await AudioSaveManager.SaveAsync(DATA_KEY, _audioSource.clip, gameObject.GetCancellationTokenOnDestroy());
    }

    public void Load()
    {
        _audioSource.clip = AudioSaveManager.Load(DATA_KEY);
    }

    public async Task LoadAsync()
    {
        _audioSource.clip = await AudioSaveManager.LoadAsync(DATA_KEY, gameObject.GetCancellationTokenOnDestroy());
    }
}
```

**See also this page: [Tarsave - Gitbook](https://tariksavas.gitbook.io/tarsave)**