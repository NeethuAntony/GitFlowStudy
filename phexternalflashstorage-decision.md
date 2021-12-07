# 1. PersistentStorage implementation for PH board decision record

Date: 2021-07-12

# Status

Accepted

# Team

Technical Architect: Abhinav Kumar <br/>
Team Lead: Amit Kumar <br/>
Real pump Technical Support: Raveen Ready, Sundaresan <br/>
Developer: Neethu Antony <br/>
Reviewer: Abhinav Kumar, Raveen Ready, Sundaresan, Amit Kumar <br/>

# Context

TherapyManager actor wants to store/read data to PH External Flash on real pump and to file in linux VM (simulator).
The idea is to have TherapyManager actor code to save data should be platform independent. 

# Decision

Implement the PersistentStorage interface for PH external flash (PHExternalFlashStorage class). The PersistentStorage already exist for storing data to 
file using FileStorage on linux VM and yocto linux (UI board).

FileStorage provides read and write api to any offset.

In PHExternalFlashStorage offset used for read/write should be always aligned to the sector size of external flash. Current sector size 
of PH external flash is 4096 bytes, so allowed offsets are 0, 4096, 8192 etc. (Multiple of 4096 bytes.)

To provide the erase before write and after read we need to go with offset size limitation.

To remove this limitation, we need to provide a public api for erase so that we can read/write to any offset.
Challenge to add public api for erase is that we need to add this api in PersistentStorage interface that take the total size of data to be erased. 
Or we need to erase full size of PH external flash (20kb). This may lead to unwanted erase of data and will be time consuming on power cycle scenario.
Also the developer take care from where the erase should be invoked other wise it can lead to unexpected issues. 

In future if we require public api for erase we can implement the same.

# Consequences

We need to go with the limitation mentioned below:
*	Offset used for read/write should be always aligned to sector size of flash device bytes boundary.
*	Continuous read/write without the restriction in offset size is not possible.

