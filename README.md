# MmUnloadedDrivers
Clear your driverobject from MmUnloadedDrivers in order to combat EAC/BE Detection Vector


```C
NTSTATUS clearTableEntry(PDRIVER_OBJECT driver, wchar_t* driverName) {
	__try
	{

		//check first entry
		PLDR_DATA_TABLE_ENTRY currentEntry = (PLDR_DATA_TABLE_ENTRY)(driver->DriverSection);
		PLDR_DATA_TABLE_ENTRY BeginningEntry = currentEntry;

		while ((PLDR_DATA_TABLE_ENTRY)(currentEntry->InLoadOrderLinks.Flink) != BeginningEntry)
		{
			if (!(ULONG)currentEntry->EntryPoint > MmUserProbeAddress)
			{
				currentEntry = (PLDR_DATA_TABLE_ENTRY)(currentEntry->InLoadOrderLinks.Flink);
				continue;
			}
			if (wcsstr(currentEntry->BaseDllName.Buffer, driverName)) {
				kOutput("Found!\n");
				//matched, set basedllname.length to 0
				//MiRememberUnloadedDriver wont add to MmUnloadedDrivers if the name length is <= 0
				currentEntry->BaseDllName.Length = 0;
				return STATUS_SUCCESS;
			}
			currentEntry = (PLDR_DATA_TABLE_ENTRY)(currentEntry->InLoadOrderLinks.Flink);
		}

		return STATUS_UNSUCCESSFUL;
		
	}

	__except
		(1)
	{
		return STATUS_UNSUCCESSFUL;
	}
}
```
