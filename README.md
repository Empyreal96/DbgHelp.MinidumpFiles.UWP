# DbgHelp.MinidumpFiles.UWP
An attempt to convert the original over to UWP, Allows access to `dbghelp.dll` features and APIs. Original library here: https://github.com/GregTheDev/MinidumpExplorer

### How I use this library:

```
using DbgHelp.MinidumpFiles;
using System;
using System.IO;
using Windows.Storage;
using Windows.Storage.Pickers;

namespace MinidumpExample
{
	public sealed partial class MainPage : Page
	{
		public MainPage()
		{
			this.InitializeComponent();
			Example();
		}

		//Initialize  
		public static MiniDumpFile _miniDumpFile { get; set; }
		public async void Example()
		{
			//Select file
			FileOpenPicker picker = new FileOpenPicker();
			picker.FileTypeFilter.Add(".dmp");
			StorageFile storageFile = await picker.PickSingleFileAsync();

			//Convert to needed stream
			var fileStream = storageFile.CreateSafeFileHandle(FileAccess.Read);
			FileStream fsStream = new FileStream(fileStream, FileAccess.Read);
			//Parse the minidump stream
			_miniDumpFile = MiniDumpFile.OpenExisting(fsStream);

			//Fetch Loaded Modules
			MiniDumpModule[] moduleData = _miniDumpFile.ReadModuleList();

			string ModuleData = "";
			foreach (var module in moduleData)
			{
				try
				{

					ModuleData += $"{module.PathAndFileName}\n" +
						$"File Size: {module.SizeOfImageFormatted}\n" +
						$"File Version: {module.FileVersion}\n" +
						$"Address: {module.BaseOfImageFormatted}\n\n";
				}
				catch (Exception ex)
				{

				}
			}

		}
	}
}
	
```

### Options

```
MiniDumpModule[] moduleData = _miniDumpFile.ReadModuleList();
MiniDumpHandleDescriptor[] handleData = _miniDumpFile.ReadHandleData();
MiniDumpExceptionStream exceptionStream = _miniDumpFile.ReadExceptionStream();
MiniDumpThread[] threadData = _miniDumpFile.ReadThreadList();
MiniDumpMemoryDescriptor[] memoryData = _miniDumpFile.ReadMemoryList();
MiniDumpSystemInfoStream systemInfo = _miniDumpFile.ReadSystemInfo();
MiniDumpSystemMemoryInfo systemMemoryInfo = _miniDumpFile.ReadSystemMemoryInfo();
MiniDumpMiscInfo miscInfo = _miniDumpFile.ReadMiscInfo();
MiniDumpCommentStreamW commentWStream = _miniDumpFile.ReadCommentStreamW();
MiniDumpCommentStreamW commentAStream = _miniDumpFile.ReadCommentStreamA();

```

