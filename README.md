# Deluge-Null-Check-Tips
Create error-free Deluge scripts with null checks!

## Core Idea
Safeguarding your script with sufficient null checks is a discipline that every Zoho developer should cultivate. Imagine the frustration when troubleshooting a long and complex script only to find out that the errors were caused certain null values. Why go through the headache of fixing it when you can avoid it? When scipting, make it a habit to ask yourself this question - will this value ever be null? If it's a yes, that's a place for a null check! Here are 3 tips and best practices to help you kickstart the habit.

### Tip #1
It's often easy to write `record.get("Lookup_Field").get("id")` when you're trying to get the ID of a lookup field on a record. But if the lookup field on the record is null, your script will fail at `.get("id")`. Here's a simple method of avoiding that:
* Put your `get()` function in an `ifNull()` shell.
* Create a map of with the "id" is the key and "none" as the value ("none" is arbritrary, you could simply put an empty string).
* Add `get("id")` after the `ifNull()` shell - this way, the value is null, you're going to get "none" as the "id" and the function will not error.
* Write your subsequent action in an `if` validation that only executes if the "id" is not "none".

```javascript
lookupID = ifNull(record.get("Lookup_Field"),{"id":"none"}).get("id");
if (lookupID != "none")
{
	//Write your subsequent actions here
}
```
#### Other Usage:
The `ifNull()` function is also useful when you are getting certain fields values to be populated somewhere else. 
* For example, Contact Name is a CRM string field which I need to get and populate in a Writer Doc.
* I would write `contactName = ifNull(record.get("Contact_Name"),"Client");`
* This way, instead of printing *null* in the merge field, it will print *Client*.

### Tip #2
When performing a `zoho.crm.searchRecords` function, we need to take into account that sometimes there will not be a match in the search. Without a null check in place, your subsequent actions that depend on the search record will fail. The search record Deluge task returns a list variable. The `size()` and `isEmpty()` can be used to validate against a list.
* After the search record function, use `if (contact.size() > 0` as the condition for your subsequent search record related actions.
 * Alternatively, you could also use `if (!contact.isEmpty())` which achieves the same objective.
* This will prevent the script from trying to `get()` empty values which results in failure.

```javascript
email = "hello@theworkflowacademy.com";
contact = zoho.crm.searchRecords("Contacts", "Email:equals:"+email);
if (contact.size() > 0)
{
	//Write your subsequent actions here
}
```
### Tip #3
When you `get()` a string field from Zoho CRM and there's no value in that field, it will return *null*. Whereas in Zoho Creator, it returns an empty string "". Zoho Invoice/Books has a rather peculiar way of dealing with null values for custom fields. When there is no value in a custom field, the field name simply does not show up in the custom field map in the record. When it comes to writing null checks, it is important to know how Zoho returns null values for different apps.
* Zoho CRM: Validate with *null*.
```javascript
if (field != null)
{
	//Write your subsequent actions here
}
```
* Zoho Creator: Validate with empty string "".
```javascript
if (field != "")
{
	//Write your subsequent actions here
}
```
* Zoho Invoice/Books: Validate with `containKey()`.
```javascript
if (record.get("custom_field_hash").containKey("Custom_Field"))
{
	//Write your subsequent actions here
}
```
*Note: All Zoho Invoice/Books custom fields will be stored in a "custom_field_hash" map in the record info.*
