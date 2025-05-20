
You cannot index directly into a pointer. To fix this, ensure that you either:

De-reference the pointer with `(*p)` to access the struct or slice