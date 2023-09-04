
# Vehicle Verification

Vehicle verification is a example app build in DAML

### I. Overview 
The vehicle verification project is used to verify the history of the vehicle during its change of ownership or the process of removing defects.

The owner of the vehicle can Vehicle contract.
The owner can sell the vehicle to another owner creating a contract called SellTransaction, and the future ownership can Accept or Reject the transaction. The owner can Cancel the transaction.
The vehicle can break down and after that, only the owner of the vehicle can create a new contract called RepairProcess with a request to the mechanic to start the process of repairing, but only the mechanic can Accept or Reject this process. After confirmation by the mechanic, the repair process changes the status to Processing. While the repair process is in progress, the Mechanic can add broken items(AddRepairItem) to the registry. After repair, the mechanic changed the repair progress to Finished and only of the owner of this vehicle can confirm the fix.
After that mechanic can create a VehicleRepairs contract which contains a registry of repairs for this vehicle with costs. Next vehicle can be used or sold.

### II. Workflow

    * Alice - the first owner of the vehicle
    * Bob - the second owner of the vehicle
    * Charlie - car shop - mechanic

                                            ####################
                                            #### Happy path ####
                                            ####################

        1.  Alice creates a new vehicle contract
        
        2.  Alice wants to sell the vehicle to Bob:
          > Alice exercises CanSellTheVehicle to check if the vehicle can be sold
          > Alice exercises Sell to Bob, and the new contract SellTransaction is created with the actual state of the vehicle and price
        
        3.  Bob accepts the transaction
          > Bob exercises Accept the request and he became the new owner of the vehicle
        
        4. Bob's vehicle broke down
          > Bob exercises ChangeVehicleState to set as the vehicle is broken
          > Without this action, anyone of mechanic can't repair the vehicle

        5.  Bob sends the request to the shop (mechanic)
          > Bob exercises Repair request to a mechanic with details about the repair
          > Contract RepairProcess is created with status Pending - waiting for a mechanic decision
        
        6.  Charlie as a mechanic accepts bob request
          > Charlie exercises AcceptRepair and the status of the contract RepairProcess changed to Processing
          > Charlie fixing a vehicle

        7.  Charlie changes the first broken element
          > Charlie exercises AddRepairItem to add the first repair item to the list with the cost

        10. Charlie finish repair
          > Charlie exercises FinishRepair to change the status of the repair process contract to Finished and 
          > The cost of these repaired items is calculated
        
        11. Bob check the repair
          > Bob exercises ConfirmRepair after positive verification of the repair process

        12. Finish the repair process and the vehicle can be used or can be sold
          > Charlie exercises AddRepairToRegistry to create a new contract VehicleRepairs - global registry of repairs
          > Bob as an owner exercises ChangeVehicleState after the repair process and changes the vehicle state to is not broken

                                            ######################
                                            #### Unhappy path ####
                                            ######################
        1.  Try selling a vehicle
            > Alice can't exercise Sell with the wrong odometer

        2.  Cancel transaction
          > Bob can't exercise Cancel on a SellTransaction contract because he isn't an owner of this vehicle

        3.  Repair request
          > Bob can't exercise Repair to create a new contract RepairProcess because the vehicle isn't broken

        4.  Add to the registry
          > Charlie (the mechanic) can't create a new contract VehicleRepairs before the confirmation repair process by the owner


### III. Challenge(s)
* The project was created by using `empty-skeleton` and the following was removed from `daml.yaml`:

```
sandbox-options:
   - --wall-clock-time
```
* The latest version 2.6.5 has been used
* A new approach to functions has been implemented

### IV. Compiling & Testing
To compile and test, run the pre-written script in the ```VehicleTest.daml ```
under /daml OR run:
```
$ daml start
