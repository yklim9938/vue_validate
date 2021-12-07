
# vue-form-validation

## Basic Usage

Wrap your form in &lt;Form&gt; component.  Add `class="orca"` to the inputs that you wish to validate. The &lt;Form&gt; will automatically picks up attributes such as `required`, `minlength`, ...etc and perform validation on your inputs. 

```html
<Form :onSubmit="submitHandler">
    <div class="mb-3">
	    <label class="form-label">Email</label>
	    <input class="orca" type="text" name="email" required validemail autocmplete="off">
	    </div>
	    <div class="mb-3">
		    <label class="form-label">Password</label>
		    <input class="orca" type="password" name="password" required minlength="6" autocmplete="off" >
		</div>
		<button type="submit" class="btn btn-primary">Submit</button>
</Form>
```

## Validation Attributes
1. `required`
2. `minlength`
3. `maxlength`
4. `min`
5. `max`
6. `accept`
7. `equalto`  - Check whether the input's value is equal to another
8. `validemail` - Value must be a valid email format
9. `maxfile` - Used on `<input type="file" multiple>` to limit maximum file
10. `maxsize` - Used on `<input type="file">` to limit the file size in bytes

### `equalto` Usage
```html
<input type="password" class="orca" id="pass" equalto="cPass">
<input type="password" class="orca" id="cPass" label="Confirm Password">
```
The value of `#pass` must equal to `#cPass`, else an error will be shown:
 "Input must match with Confirm Password."
 Note that the `label` in `#cPass` is used in the validation message.

## Submit Event
Use `onSubmit` prop to handle your submit event. The `event` parameter will be passed. You don't have to run `e.preventDefault()` as the &lt;Form&gt; component already prevented default.

If one of the inputs is not valid, `event.isValid` will be `false`
```html
<Form :onSubmit="submitHandler">
```
```js
submitHandler(e) { // e is the event param
	console.log(e.isValid) // true if valid, false if not
}
``` 

## Validation for custom input components
If you have custom input components such as datepicker, range slider,... etc, you can use the `<ErrorMessage>`
component to add validation message. Just pass in the value of your component into `<ErrorMessage>` and add in the validation attributes. E.g.
```html
<MyCustomInput v-model="myValue"></MyCustomInput>
<ErrorMessage v-model="myValue" minlength="4" required></ErrorMessage>
```

## Changing validation message & Adding custom validation
In /assets/js/validation.js, you can change the default message in `MESSAGES`, or add/modify validation in `rules`