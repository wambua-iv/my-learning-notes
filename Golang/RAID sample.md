
```go 
package main

import (
	"fmt"
	"log"
)

// Disk represents a single disk in the RAID array
type Disk struct {
	data []byte
}

// RAID represents a RAID array
type RAID struct {
	disks []Disk
}

// NewRAID initializes a new RAID array with the specified number of disks
func NewRAID(numDisks int) *RAID {
	disks := make([]Disk, numDisks)
	for i := range disks {
		disks[i] = Disk{data: make([]byte, 0)}
	}
	return &RAID{disks: disks}
}

// Write writes data to the RAID array, distributing it across the disks
func (r *RAID) Write(data []byte) {
	for i, b := range data {
		r.disks[i%len(r.disks)].data = append(r.disks[i%len(r.disks)].data, b)
	}
}

// Read reads data from the RAID array
func (r *RAID) Read() []byte {
	var result []byte
	for _, disk := range r.disks {
		result = append(result, disk.data...)
	}
	return result
}

// Display displays the contents of the RAID array
func (r *RAID) Display() {
	for i, disk := range r.disks {
		fmt.Printf("Disk %d: %v\n", i, disk.data)
	}
}

func main() {
	raid := NewRAID(3) // Create a RAID array with 3 disks

	data := []byte("Hello, World!")
	raid.Write(data) // Write data to the RAID array

	raid.Display() // Display the contents of the RAID array

	readData := raid.Read() // Read data from the RAID array
	fmt.Printf("Read Data: %s\n", readData)
}

```