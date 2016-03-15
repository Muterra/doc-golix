# Implementation notes

## Persistence providers -- state-based

### GIDC

1. Verify object unpacks correctly (therefore, correct file hash)
2. Add to known authors and object store if successful

### GEOC

1. Verify object unpacks correctly
2. Verify object author is known to persister
3. Verify object signature
4. Verify a binding for object already exists
5. Add to object store if successful

### GOBS

1. Verify object unpacks correctly
2. Verify object author is known to persister
3. Verify object signature
4. Verify no pre-existing debinding for GOBS address exists (NOT the target)
5. Add to object store if successful

### GOBD

1. Verify object unpacks correctly
2. Verify object author is known to persister
3. Verify object signature
4. Verify no pre-existing debinding for the GOBD dynamic address exists (NOT the target)
5. State verification:
    + If persistence provider has no existing GOBD at that dynamic address:
        1. Verify GOBD has no history
        2. Verify dynamic address is correctly constructed
    + If persistence provider has an existing GOBD at that dynamic address:
        1. Verify author of new frame matches existing author
        2. Verify history of new frame contains (at least) most recent existing frame
6. Add to object store if successful

### GDXX

1. Verify object unpacks correctly
2. Verify object author is known to persister
3. Verify object signature
4. Verify no pre-existing debinding for GDXX address exists (NOT the target)
5. Verify author/recipient is same as referenced object
6. Add to object store if successful

### GARQ

1. Verify object unpacks correctly
2. Verify object recipient is known to persister
3. Verify no pre-existing debinding for GARQ address exists (NOT the target)
4. Add to object store if successful
