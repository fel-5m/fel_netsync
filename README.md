# NetSync Script for FiveM

## Description

This FiveM script provides a robust system for executing native methods on entities such as vehicles, peds, and objects, ensuring that these operations are properly synchronized across the network. The script uses server-client communication to request control of entities when necessary and execute methods even if the player does not have direct control.

## Features

- **Network Synchronization**: Automatically handles network control of entities before executing methods.
- **Extensive Native Method Support**: Supports a variety of native methods for vehicles, peds, and objects.
- **Custom Command**: Includes an example command to demonstrate how to explode the nearest vehicle.

## Methods Included

The following methods are available through the `NetSync` object:

### Vehicles
- `DeleteVehicle(vehicle)`
- `SetVehicleTyreBurst(vehicle, index, onRim, dmg)`
- `SetVehicleDoorShut(vehicle, doorIndex, closeInstantly)`
- `SetVehicleDoorOpen(vehicle, doorIndex, loose, openInstantly)`
- `SetVehicleDoorBroken(vehicle, doorIndex, deleteDoor)`
- `SetVehicleTyreFixed(vehicle, wheelIndex)`
- `SetVehicleEngineHealth(vehicle, health)`
- `SetVehicleBodyHealth(vehicle, health)`
- `SetVehicleDeformationFixed(vehicle)`
- `SetVehicleFixed(vehicle)`
- `NetworkExplodeVehicle(vehicle, isAudible, isInvisible)`

### Peds
- `DeletePed(ped)`
- `TaskWanderInArea(ped, x, y, z, radius, minimalLength, timeBetweenWalks)`
- `TaskFollowNavMeshToCoord(ped, x, y, z, speed, timeout, stoppingRange, persistFollowing, unk)`
- `TaskGoToCoordAnyMeans(ped, x, y, z, speed, p5, p6, walkingStyle, p8)`
- `SetEntityAsNoLongerNeeded(ped)`
- `SetPedKeepTask(ped, state)`

### Objects
- `DeleteObject(object)`
- `DeleteEntity(entity)`

## Installation

1. Download or clone the repository.
2. Add the script to your FiveM resources directory.
3. Ensure the resource is started in your `server.cfg`:

    ```ini
    ensure fel_netsync
    ```

4. Include the `Fetch` function from the script in any other resource where you need to use the network synchronization:

    ```lua
    local NetSync = exports.fel_netsync:Fetch()
    ```

## Example Usage

```lua
local NetSync = exports.fel_netsync:Fetch()

RegisterCommand('deleteVehicle', function(source, args, rawCommand)
    local playerPed = PlayerPedId()
    local playerCoords = GetEntityCoords(playerPed)
    
    local closestVehicle = GetClosestVehicle(playerCoords.x, playerCoords.y, playerCoords.z, 10.0, 0, 71)
    
    if closestVehicle and DoesEntityExist(closestVehicle) then
        NetSync:DeleteVehicle(closestVehicle)
        print("Vehicle deleted")
    else
        print("No vehicle found")
    end
end, false)

RegisterCommand('deleteObject', function(source, args, rawCommand)
    local playerPed = PlayerPedId()
    local playerCoords = GetEntityCoords(playerPed)
    
    local closestObject = GetClosestObjectOfType(playerCoords.x, playerCoords.y, playerCoords.z, 10.0, GetHashKey(args[1]), false, false, false)
    
    if closestObject and DoesEntityExist(closestObject) then
        NetSync:DeleteObject(closestObject)
        print("Object deleted")
    else
        print("No object found")
    end
end, false)

RegisterCommand('deletePed', function(source, args, rawCommand)
    local playerPed = PlayerPedId()
    local playerCoords = GetEntityCoords(playerPed)
    
    local closestPed = GetClosestPed(playerCoords.x, playerCoords.y, playerCoords.z, 10.0, 1, 0, 0, 0, 26)
    
    if closestPed and DoesEntityExist(closestPed) then
        NetSync:DeletePed(closestPed)
        print("Ped deleted")
    else
        print("No ped found")
    end
end, false)

RegisterCommand('explodeVehicle', function(source, args, rawCommand)
    local playerPed = PlayerPedId() 
    local playerCoords = GetEntityCoords(playerPed)
    
    local closestVehicle = GetClosestVehicle(playerCoords.x, playerCoords.y, playerCoords.z, 10.0, 0, 71)

    if closestVehicle and DoesEntityExist(closestVehicle) then
        NetSync:NetworkExplodeVehicle(closestVehicle, true, false)
        print("Vehicle exploded")
    else
        print("No vehicle found")
    end
end, false)
