# Common Errors & Solutions

## 1. **Edit Dialog State Issue in `ServiceDialog`**
**Error:**
```js
<ServiceDialog
  open={createDialogOpen}
  onOpenChange={setCreateDialogOpen}
  onSuccess={refetch}
  id={service._id}
  trigger={
    <Button variant="ghost" size="sm">
      <Pencil className="h-4 w-4" />
    </Button>
  }
/>
```

**Issue:**
- The `createDialogOpen` state was being used for all dialog instances, leading to multiple dialogs opening incorrectly.

**Solution:**
```js
<ServiceDialog
  open={editDialogOpen === service._id}
  onOpenChange={(state) => setEditDialogOpen(state ? service._id : null)}
  onSuccess={refetch}
  id={service._id}
  trigger={
    <Button variant="ghost" size="sm">
      <Pencil className="h-4 w-4" />
    </Button>
  }
/>
```
- Now, each dialog opens for its specific service.

---

## 2. **Incorrect `refetch` Usage**
**Error:**
```js
await refetch;
```

**Issue:**
- `refetch` is a function, and calling it without `()` does nothing.

**Solution:**
```js
await refetch();
```
- Always invoke it properly as a function.

---

## 3. **GraphQL Mutation Error - Undefined Variables**
**Error:**
```js
const { data } = await deleteServiceById({
  variables: { _id },
});
```

**Issue:**
- `_id` was undefined or missing, causing the mutation to fail.

**Solution:**
```js
const { data } = await deleteServiceById({
  variables: { _id: selectedServiceId },
});
```
- Ensure `_id` has a valid value before making the request.

---

## 4. **React State Issue - Updating UI After Mutation**
**Error:**
```js
const handleDelete = async () => {
  await deleteServiceById({
    variables: { _id: selectedServiceId },
  });
  toast.success("Deleted Successfully");
  setDeleteDialogOpen(false);
};
```

**Issue:**
- The UI wasn’t updating after deletion because `refetch()` wasn’t called.

**Solution:**
```js
const handleDelete = async () => {
  const { data } = await deleteServiceById({
    variables: { _id: selectedServiceId },
  });

  if (data?.deleteServiceById?.success) {
    toast.success(data.deleteServiceById.message);
    await refetch();
  } else {
    toast.error("Failed to delete service");
  }
  setDeleteDialogOpen(false);
};
```
- Always call `refetch()` after mutations to update the UI.

---

## 5. **GraphQL Query - `data` Undefined Check**
**Error:**
```js
const services = data.getAllServices.data;
```

**Issue:**
- If `data` is undefined during loading, it will cause an error.

**Solution:**
```js
const services = data?.getAllServices?.data || [];
```
- Using optional chaining prevents runtime errors.

---

## 6. **Switch Component Issue - Incorrect State Update**
**Error:**
```js
<Switch
  checked={service.status}
  onCheckedChange={(checked) => handleToggle(service._id, checked)}
/>
```

**Issue:**
- `service.status` is a string (`"active"` or `"inactive"`), but `checked` expects a boolean.

**Solution:**
```js
<Switch
  checked={service.status === "active"}
  onCheckedChange={(checked) => handleToggle(service._id, "status", checked)}
/>
```
- Converts `status` to a boolean for correct behavior.

---

## 7. **Event Handling in Search Input**
**Error:**
```js
onChange={setSearch(e.target.value)}
```

**Issue:**
- `setSearch` is being called immediately instead of being passed as a function.

**Solution:**
```js
onChange={(e) => setSearch(e.target.value)}
```
- Wrap it inside an arrow function to delay execution.

---

This file will be updated as more errors are encountered! 🚀