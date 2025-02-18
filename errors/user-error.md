git # Fixing Undefined "data" Argument Error in createUser Function

## Issue Explanation
The error message:
```json
{
  "success": false,
  "message": "The \"data\" argument must be of type string or an instance of Buffer, TypedArray, or DataView. Received undefined"
}
```
indicates that the function is trying to access `data` (or `args`) but is receiving `undefined`. This issue often occurs due to incorrect argument handling in JavaScript functions, particularly in GraphQL resolvers or database operations.

## Before Fixing the Issue
### **Problems in the Initial Approach**
1. **Order of Operations**: The function hashes the password before checking if the phone number already exists, potentially performing unnecessary computations.
2. **Salt & Password Scope**: The `salt` and `hashedPassword` are defined inside an `if` block but later referenced outside it, leading to possible `undefined` values.
3. **Modifying `args` Inline**: The function assigns values to `args` properties directly without ensuring that `args` remains immutable, which can lead to unexpected behaviors.
4. **Error Handling**: The function rethrows errors as new `Error` instances, which can strip away the original stack trace.

```javascript
async function createUser(args) {
  try {
    if (args.password) {
      const salt = randomBytes(32).toString("hex");
      const hashedPassword = generateHash(salt, args.password);
    }

    if (args.phone) {
      const userExistByPhone = await getUserByPhone(args.phone);
      if (userExistByPhone) {
        throw new Error("Phone number already exists");
      }
    }

    const otp = Math.floor(100000 + Math.random() * 900000).toString();
    console.log(otp);
    
    await UserModel.create({
      ...args,
      salt: salt || "",
      otp,
      otp_expiry: Date.now() + 3600000,
      password: hashedPassword || "",
    });

    return {
      success: true,
      message: "User created successfully",
    };
  } catch (error) {
    throw new Error(error.message);
  }
}
```

## Solution: Properly Structuring `args` Before Use
### **Improvements in the Updated Approach**
1. **Phone Check First**: The function first checks if the phone number already exists before processing any other data.
2. **Controlled `args` Mutations**: Instead of modifying `args` inline, a new object is created to ensure data integrity.
3. **Scoped `salt` & `hashedPassword`**: These variables are correctly assigned within the block where they are needed.
4. **Better Error Handling**: The function directly throws caught errors instead of wrapping them unnecessarily.

```javascript
async function createUser(args) {
  try {
    if (args.phone) {
      const userExistByPhone = await getUserByPhone(args.phone);
      if (userExistByPhone) {
        throw new Error("Phone number already exists");
      }
    }

    if (args.password) {
      const salt = randomBytes(32).toString("hex");
      const hashedPassword = generateHash(salt, args.password);
      args = {
        ...args,
        salt,
        password: hashedPassword,
      };
    }

    const otp = Math.floor(100000 + Math.random() * 900000).toString();
    console.log(otp);
    
    await UserModel.create({
      ...args,
      otp,
      otp_expiry: Date.now() + 3600000,
    });

    return {
      success: true,
      message: "User created successfully",
    };
  } catch (error) {
    throw error;
  }
}
```

## Key Fixes and Benefits
- ✅ **Ensures `args` is structured correctly before use**
- ✅ **Prevents unnecessary computations**
- ✅ **Avoids modifying function arguments directly**
- ✅ **Improves readability and maintainability**
- ✅ **Better error handling to preserve original stack trace**

---

## **Note:**
If using GraphQL, ensure that `createUser` resolver properly extracts arguments, e.g.,:
```javascript
createUser: async (parent, { data }, context, info) => {
  return createUser(data);
}
```
This avoids issues where `args` might be `undefined` due to incorrect GraphQL mutation structure.


