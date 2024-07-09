# FisoThemes' Generics Library for TwinCAT

## Overview

A library designed to provide generic data handling for TwinCAT environments. Allowing for flexible and type-safe manipulation of data across different data types, enhancing code reusability and flexibility.

## Features

- **Generic Data Handling:** Enables manipulation of data without specifying the specific data type beforehand.
- **Type Safety:** Ensures type safety during generic operations, preventing type mismatch errors.
- **Memory Management:** Includes utilities for memory allocation and deallocation for generic data types.
- **Data Conversion:** Facilitates conversion between various data types using generic interfaces.

## Dependencies

FsGenerics depends on the following libraries:
- **[FsCommon](https://github.com/fisothemes/FisoThemes-Common-Library-for-TwinCAT) :** Provides common data structures and utilities.
- **[FsError](https://github.com/fisothemes/FisoThemes-Error-Library-for-TwinCAT) :** Manages error handling and error code definitions.

## Usage

The library provides a set of functions and utilities that can be integrated into TwinCAT projects to handle generic data effectively. Below are some examples of typical usage scenarios:

### Example 1: Allocating Memory for Generic Data
```js
// Allocate memory for a generic value of type T_Generic (__SYSTEM.AnyType).
VAR
    pGenericValues  : POINTER TO FsCommon.T_Generic;
    Error           : FsError.T_Error;
    fbWithAdsLogger : FsError.FB_AdsErrorLogger;
END_VAR

...

pGenericValues := F_AllocateMemoryGeneric(10, Error => Error); // Allocate memory for 10 elements of T_Generic.
IF Error.IsRaised THEN
    Error.LogMessage(fbWithAdsLogger); // Log error to ADS logger.
    // Handle memory allocation error.
    ...
    END_IF

...

```

### Example 2: Searching for an Item in a Generic List
```js
// Searches for an item in the list and returns its index.
// [note] An index of -1 = Not Found.
METHOD PUBLIC Search : I_List // A reference to the current instance.
VAR_INPUT
    Item    : ANY; // Item to find in the list.
END_VAR
VAR_OUTPUT
    Index   : FsCommon.T_Position := -1; // Location of the item in the list.
    Error   : FsError.T_Error; // Error information if search fails.
END_VAR
VAR
    i       : FsCommon.T_Position;
END_VAR

// Set return value to be the current instance of this Function Block.
Search := THIS^;

// Search for item.
FOR i := THIS^.Begin TO THIS^.End DO
    CASE FsGenerics.F_CompareGenerics(Item, THIS^._pGenericsBuffer[i]) OF
        FsCommon.T_Comparison.Equal: Index := i; RETURN;
        END_CASE
    END_FOR

// Generate error if item is not found.
Error := FsError.F_CreateError(
    FsCollections.GVL_ErrorDescriptors.fbErrorCodes,
    SEL(THIS^.IsEmpty,
        FsCollections.E_ErrorCodes.ERR_ITEM_NOT_FOUND,
        FsCollections.E_ErrorCodes.ERR_COLLECTION_EMPTY));

END_METHOD
```

### Example 3: Swapping Variables Using Generics
```js
VAR
    bIsFirstCall   : BOOL := TRUE;
    nValue1         : INT := 12;
    nValue2         : INT := 24;
    Error           : FsError.T_Error;
    fbToDisk        : FsError.FB_DiskErrorLogger('C:\temp\errlog.csv');
END_VAR

...

// If this is the first call swap the values.
IF FsCommon.F_ResetOnTrue(bIsFirstCall) THEN
    FsGenerics.F_SwapAny(nValue1, nValue2, Error => Error);
    // Log error to disk if swap failed.
    IF Error.IsNotRaised THEN
        // Perform some actions.
        ...
    ELSE 
        Error.LogMessage(fbToDisk); 
        // Handle error.
        ...
        END_IF
    ...
    END_IF

...

```

## Developer Notes
This project is still in development. There's a lot of work and testing ahead. Changes to functionality may occur in the future.

This is designed to be part of a larger framework that is still under development.