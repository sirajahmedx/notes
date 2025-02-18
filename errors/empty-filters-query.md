# 🚨 GraphQL Query Returning Empty Results Issue

## **Problem:**

- GraphQL query for fetching users (`getAllUsers`) returns an empty array (`data: []`) on the web but works fine on Apollo Studio Playground.
- No errors are shown, and the response indicates a successful fetch with zero results.

---

## ❌ **Wrong Approach:**

### **Issue:** Incorrect Filter Handling

```javascript
const { data } = useQuery(GetAllUsers, {
  variables: {
    filters: {
      first_name: search || "",
      role: role || "",
      status: status || "",
      skills: [skill] || [""],
    },
  },
});
```

**Problem:** `skills: [skill] || [""]` always defaults to an empty array, sending invalid filters to the backend.

---

## ✅ **Correct Approach:**

### 📝 **1. Fix Filter Handling Before Querying:**

```javascript
const filters = {};
if (search) filters.first_name = search;
if (role) filters.role = role;
if (status) filters.status = status;
if (skill) filters.skills = [skill];

const { data, refetch } = useQuery(GetAllUsers, {
  variables: {
    filters,
    limit: 10,
    page: 1,
  },
  fetchPolicy: "network-only",
});
```

### 📝 **2. Add Pagination Defaults:**

```javascript
useQuery(GetAllUsers, {
  variables: {
    filters,
    limit: 10,
    page: 1,
  },
  fetchPolicy: "network-only",
});
```

### 📝 **3. Check Backend Filter Logic:**

- Ensure backend allows queries with optional filters.
- Empty strings or arrays should not block results.

---

## 🛎️ **Debugging Tip:**

Log filters before querying:

```javascript
console.log("Applied Filters:", filters);
```

**Status:** 🟢 Issue Resolved After Fixing Filters
