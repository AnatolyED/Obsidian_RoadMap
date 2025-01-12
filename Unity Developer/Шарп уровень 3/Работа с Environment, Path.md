**Environment** - класс используется для получения информации об операционной системе, путях к файлам и каталогах специфичных для пользователя.

**Path** - класс используется для работы с путями к файлам и каталогам кроссплатформенным способом.

![[Pasted image 20250112225814.png]]
### Environment

1. Environment.CurrentDirectory - получает или задает текущий рабочий каталог.
2. Environment.MachineName - получает имя компьютера.
3. Environment.UserName - получает имя пользователя, вошедшего в систему.
4. Environment.GetFolderPath() - получает системный каталоги, такие как "Рабочий стол", "Документы" и т.д.

### Path

1. Path.Combine() - объединяет несколько строк в один путь к файлу или каталогу.
2. Path.GetFileName() - извлекает имя и расширение файла из пути.
3. Path.GetDirectoryName() - извлекает часть пути к файлу, относящуюся к каталогу.
4. Path.GetExtension() - извлекает расширение файла.

### Обработка кроссплатформенных путей

1. Windows используются обратные косые черты "\\" для путей.
2. Linux/macOS использует прямые косые черты "/".
3. Используйте метод Path.Combine() для обеспечения совместимости.
4. Path.DirectorySeparatorChar извлекает разделитель каталогов, специфичный для платформы.


### Работа с диском

Работа с дисками происходит через класс DriveInfo.

1. Фиксированные диски - основные жесткие диски, на которых установлена операционная система (DriveType.Fixed).
2. Съемные диски - USB-накопители, внешние жесткие диски (DriverType.Removable).
3. Сетевые диски - общие сетевые диски (DriveType.Network).
4. CD/DVD-приводы - оптические носители (DriverType.CDRom).

Пример:

```
static void Main()
{
	DriveInfo[] drives = DriveInfo.GetDrives();
	foreach(DriveInfo drive in drives)
	{
		if(drive.DriveType == DriveType.Removable && drive.IsReady)
		{
			Console.WriteLine("Found Removable Drive: {drive.Name}");
			string sourceFile = @"C:\example.txt";
			string destinationFile = Path.Combine(drive.Name, "example.txt");

			File.Copy(sourceFile, destinationFile);
			Console.WriteLine("File copied to removable drive.");
			break;
		}
	}
}
```
### Исключения, связанные с диском обработка ошибок

Распространенные исключения для обработки:
1. IOException - общая ошибка ввода-вывода (например, диск не готов),
2. UnauthorizedAccessException - отсутствие разрешений на доступ к диску,
3. DriveNotFoundException - диск недоступен.

Пример:

```
try
{
	// Perform file operations
}
catch (UnauthorizedAccessException ex)
{
	Console.Writeline("Access denied. Please check your permissions.);
}
catch (IOException)
{
	Console.WriteLine("An I/O error occurred: " + ex.Message);
}
```

### Класс кодировки System.Text.Encoding

![[Pasted image 20250112231823.png]]