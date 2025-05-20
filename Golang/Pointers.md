>[!Note]
>For methods with pointer receivers, do not access the values using value schematics, the method will not be included in the `method set`

 ```go 
type Printer interface {
	print()
}
type user struct {
	name string
}

u:= user {"Bill"}

entities := []Printer {
	//Store copy of the user value
	u
	//store a copy of the address of the user value
	&u
}
```

> [!tip]
> If `p` is a pointer to a struct and you want to access its fields, you need to use `p.FieldName` or dereference the pointer `(*p).FieldName`.

```go
type path []byte

func (p *path) ToUpper() {
    for i, b := range *p {
        if 'a' <= b && b <= 'z' {
            (*p)[i] = b + 'A' - 'a'
        }
    }
}

func main() {
    pathName := path("/usr/bin/tso")
    pathName.ToUpper()
    fmt.Printf("%s\n", pathName)
}


```
