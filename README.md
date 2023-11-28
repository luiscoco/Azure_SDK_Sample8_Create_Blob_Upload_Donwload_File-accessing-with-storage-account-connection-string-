# Azure_SDK_Sample9_Create_Blob_Upload_Donwload_File-accessing-with-storage-account-connection-string

## 1. Create an Azure Storage account

![image](https://github.com/luiscoco/Azure_SDK_Sample8_Create_Blob_Upload_Donwload_File/assets/32194879/8ba82175-1f6a-4fe1-a343-2caf8bc38f0a)

![image](https://github.com/luiscoco/Azure_SDK_Sample8_Create_Blob_Upload_Donwload_File/assets/32194879/f8cd86dc-3e22-4dcf-9b02-5e4ae91bee01)

## 2. Copy the connection string

![image](https://github.com/luiscoco/Azure_SDK_Sample9_Create_Blob_Upload_Donwload_File-accessing-with-storage-account-connection-string-/assets/32194879/8f502c60-16cb-4cd6-9102-4a6b8e9644a1)

## 3. Input the source code in the program.cs file

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;

// TODO: Replace <storage-account-name> with your actual storage account name
var blobServiceClient = new BlobServiceClient("<storage-account-connection-string>");

//Create a unique name for the container
string containerName = "quickstartblobs" + Guid.NewGuid().ToString();

// Create the container and return a container client object
BlobContainerClient containerClient = await blobServiceClient.CreateBlobContainerAsync(containerName);

// Create a local file in the ./data/ directory for uploading and downloading
string localPath = "data";
Directory.CreateDirectory(localPath);
string fileName = "quickstart" + Guid.NewGuid().ToString() + ".txt";
string localFilePath = Path.Combine(localPath, fileName);

// Write text to the file
await File.WriteAllTextAsync(localFilePath, "Hello, World!");

// Get a reference to a blob
BlobClient blobClient = containerClient.GetBlobClient(fileName);

Console.WriteLine("Uploading to Blob storage as blob:\n\t {0}\n", blobClient.Uri);

// Upload data from the local file
await blobClient.UploadAsync(localFilePath, true);

Console.WriteLine("Listing blobs...");

// List all blobs in the container
await foreach (BlobItem blobItem in containerClient.GetBlobsAsync())
{
    Console.WriteLine("\t" + blobItem.Name);
}

// Download the blob to a local file
// Append the string "DOWNLOADED" before the .txt extension 
// so you can compare the files in the data directory
string downloadFilePath = localFilePath.Replace(".txt", "DOWNLOADED.txt");

Console.WriteLine("\nDownloading blob to\n\t{0}\n", downloadFilePath);

// Download the blob's contents and save it to a file
await blobClient.DownloadToAsync(downloadFilePath);

// Clean up
Console.Write("Press any key to begin clean up");
Console.ReadLine();

Console.WriteLine("Deleting blob container...");
await containerClient.DeleteAsync();

Console.WriteLine("Deleting the local source and downloaded files...");
File.Delete(localFilePath);
File.Delete(downloadFilePath);

Console.WriteLine("Done");
```

