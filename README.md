# Android Style Guide

The intention of this guide is to provide a set of conventions that encourage good code.

## In General

You should strive to make code that is **easy to read**, **easy to understand**, and **easy to maintain**. That is why it is important when reviewing code to ensure these guidelines are met no matter how small, or nit-picky a request for a change may seem.

## Formatting

#### Use spaces to indent

 - Use 4 spaces
 - Don't use tabs
 - Configure your IDE to respect this

#### Remove whitespace at end of lines and files

  - Don't have extraneous whitespace at the end of lines
  - Don't have extraneous whitespace at the end of files


  It makes navigating the code difficult and can complicate diffs unnecessarily.

#### Don't prettify with indentation

    
    // Bad.
    int        i;
    String     s;

    // Good
    int i;
    String s;

Indenting this way does not scale and thus causes overtly complicated diffs. The "benefits" of aligning indentation are questionable.

#### Line length

Use a "maximum" line length of 100, set your IDE to respect this. There needs to be some limit and this limit allows for viewing multiple code files side-by-side. If you exceed this limit it is an indicator that you should perhaps refactor your code.

#### Do not break up statements unnecessarily  

    
    // Bad
    final String value =
        otherValue;

    // Good
    final String value = otherValue;

The same applies to method declarations.

    
    // Sub-optimal since line breaks are arbitrary and only filling lines.
    String downloadAnInternet(Internet internet, Tubes tubes,
        Blogosphere blogs, Amount<Long, Data> bandwidth) {
      tubes.download(internet);
      ...
    }

    // Acceptable, but may be awkward depending on the column depth of the opening parenthesis.
    public String downloadAnInternet(Internet internet,
                                     Tubes tubes,
                                     Blogosphere blogs,
                                     Amount<Long, Data> bandwidth) {
      tubes.download(internet);
      ...
    }

    // Preferred for easy scanning and extra column space.
    public String downloadAnInternet(
        Internet internet,
        Tubes tubes,
        Blogosphere blogs,
        Amount<Long, Data> bandwidth) {

      tubes.download(internet);
      ...
    }

The same applies to chained method calls.

    
    // Bad.
    //   - Line breaks are based on line length, not logic.
    Iterable<Module> modules = ImmutableList.<Module>builder().add(new LifecycleModule())
        .add(new AppLauncherModule()).addAll(application.getModules()).build();

    // Good.
    //   - Method calls are isolated to a line.
    //   - The proper location for a new method call is unambiguous.
    Iterable<Module> modules = ImmutableList.<Module>builder()
        .add(new LifecycleModule())
        .add(new AppLauncherModule())
        .addAll(application.getModules())
        .build();

#### Always use curly braces with conditionals

    
    // Bad
    while (op(i)) ++i;

    // Bad
    while (op(i)) 
        ++i;

    // Good
    while (op(i)) {
      ++i;
    }

A block is more explicit and offers less chance for mistakes. A block is easier to read and easier to change.

#### Use one-liners (sparingly)

    
    // This is OK
    public String getName() {
        return name;
    }

    // This is arguably better
    public String getName() { return name; }

#### Prefer ?: operator over conditionals for assignment operations

    
    // Bad
    final String jsonString;
    if (path.startsWith("resource://")) {
        jsonString = loadResource(path.replaceFirst("resource://", ""));
    } else {
        jsonString = loadFile(path);
    }

    // Good
    final String jsonString = path.startsWith("resource://")
        ? loadResource(path.replaceFirst("resource://", ""))
        : loadFile(path);

It becomes clear that this is an assignment operation, takes less vertical space and allows conditionals to be only used for flow control.

#### Collapse conditional logic

    
    // Bad
    if (value > MAX) {
        return true;
    } else {
        return false;
    }

    // Good
    return value > MAX;

#### Spaces around braces

    
    // Bad
    if(x>y){
        doSomething();
    }else{
        doSomethingElse();
    }

    // Good
    if (x > y) {
        doSomething();
    } else {
        doSomethingElse();
    }

#### Don't use brackets in a return statement

    
    // Bad
    return (x > y);

    // Good
    return x > y;

#### Use braces to assert order of operation

    
    // Bad
    return a << 8 * n + 1 | 0xFF;

    // Good.
    return (a << (8 * n) + 1) | 0xFF;
    
## Comments

#### Use comments sparingly

Only comment if the comment adds value, if a comment is needed there's possibly something wrong with your code.

    
    // Bad
    // Returns the name
    public String getName() { ... }

    // Acceptable
    public void close() {
        // clearCache needs to be called before disconnecting or 
        // the cache will be used next time the socket is connected
        clearCache();
        socket.disconnect();
    }

#### Comment correctly

  - Don't use block commenting via /* and */ use line comments only.
  - Have a space after the //


 
    // Bad
    /*
      This is a comment!
    */

    // Bad
    //No space before the comment

    // Good
    // This comment
    // is over
    // several lines

This makes it easier to uncomment a single line, or see when using a diffing tool what is and isn't a comment.

#### Don't commit commented out code

  - If the code is so important, why is it commented out?
  - It creates unease amongst other developers -- what is this code?
  - It goes against the purpose of source control
  - It creates noise and adds no value

## Field, class, and method declarations

#### Clean imports

  - Let your IDE do automatic import cleanup if it can.
  - Only import classes that are used.
  - Order import alphabetically.
  - Do not use wildcard imports (i.e. com.framework.abc.*)


#### Accessibility modifiers

All classes, methods and fields should have explicit accessibility modifiers (public, protected, private or package-private). There is no keyword for package-private accessibility, therefore it is required to add a comment to denote package-private accessibility.

    
    // package-private
    int count;
    
#### Modifier order

We follow the [Java Language Specification](http://docs.oracle.com/javase/specs/) for modifier
ordering (sections
[8.1.1](http://docs.oracle.com/javase/specs/jls/se7/html/jls-8.html#jls-8.1.1),
[8.3.1](http://docs.oracle.com/javase/specs/jls/se7/html/jls-8.html#jls-8.3.1) and
[8.4.3](http://docs.oracle.com/javase/specs/jls/se7/html/jls-8.html#jls-8.4.3)).

    
    // Bad.
    final volatile private String value;

    // Good.
    private final volatile String value;

#### Declaration order

List fields in order from most-accessible to least accessible.

    
    // Bad
    private String s;
    public int i;
    protected BigDecimal bd;
    // package-private
    long l;
    private final float f = 1.0f;
    public final double d = 1.0d;

    // Good
    public final double d = 1.0d;
    public int i;

    protected BigDecimal bd;

    // package-private
    long l;

    private final float f = 1.0f;
    private String s;

#### Limit visibility

Limit the visibility as much as possible, only expose what you intend a caller to have access to. Don't expose internal variables, expose them via methods.

#### The final modifier

Use final almost everywhere, make as many things final as possible. That restriction forces to write code that has immutability. Please note, a final reference does not prevent the object being referenced from changing.

## Naming

#### Name variables to convey meaning

Extremely short variable names should be reserves for counts and iterators. A variable name should be understand without any extra context.

    
    // Bad
    private final int a;
    private final String m;

    // Good
    private final int ageInYears;
    private final String userName;

#### Include units in variable names

    
    // Bad.
    long pollInterval;
    int fileSize;

    // Good.
    long pollIntervalMs;
    int fileSizeGb.

#### Don't embed metadata in variable names
A variable name should describe the variable's purpose.  Adding extra information like scope and type is generally a sign of a bad variable name.

Avoid embedding the field type in the field name.

    
    // Bad.
    Map<Integer, User> idToUserMap;
    String valueString;

    // Good.
    Map<Integer, User> usersById;
    String value;

#### Don't use Hungarian notation

    
    // Bad
    String _value;
    String mValue

    // Good
    String value;

#### camelCase variables and methods, SHOUTY_CASE for constants

    
    // Bad
    private String FirstName = "name";
    private final String myConstant = "a";
    public void DoSomething() { ... }

    // Good
    private String firstName = "name";
    private final String MY_CONSTANT = "a";
    public void doSomething() { ... }

#### CamelCase for classes

    
    // Bad
    public final class myClass {
    public final class MY_CLASS {

    // Good
    public final class MyClass (

#### All lowercase and singular for package names

    
    //Bad
    no.bakkenbaeck.WebSocket
    no.bakkenback.websockets
    
    // Good
    no.bakkenbaeck.websocket

#### Class files

Any classes that you define should be named using UpperCamelCase, for example:

	AndroidActivity, NetworkHelper, UserFragment, PerActivity


Any classes extending an Android framework component should **always** end with the component name. For example:

	UserFragment, SignUpActivity, RateAppDialog, PushNotificationServer, NumberView

#### Resource files

When naming resource files you should be sure to name them using lowercase letters and underscores instead of spaces, for example:

	activity_main, fragment_user, item_post

#### Drawable files

Drawable resource files should be named using the **ic_** prefix along with the size and color of the asset. For example, white accept icon sized at 24dp would be named:

	ic_accept_24dp_white

And a black cancel icon sized at 48dp would be named:

	ic_cancel_48dp_black

We use this naming convention so that a drawable file is recognisable by its name. If the colour and size are not stated in the name, then the developer needs to open the drawable file to find out this information.

Other drawable files should be named using the corresponding prefix, for example:

| Type       | Prefix    | Example                |
|------------|-----------|------------------------|
| Selector   | selector_ | selector_button_cancel |
| Background | bg_       | bg_rounded_button      |
| Circle     | circle_   | circle_white           |
| Progress   | progress_ | progress_circle_purple |
| Divider    | divider_  | divider_grey           |

When creating selector state resources, they should be named using the corresponding suffix:

| State    | Suffix    | Example             |
|----------|-----------|---------------------|
| Normal   | _normal   | btn_accept_normal   |
| Pressed  | _pressed  | btn_accept_pressed  |
| Focused  | _focused  | btn_accept_focused  |
| Disabled | _disabled | btn_accept_disabled |
| Selected | _selected | btn_accept_selected |


#### Layout files

When naming layout files, they should be named starting with the name of the Android Component that they have been created for. For example:

| Component        | Class Name      | Layout Name       |
|------------------|-----------------|-------------------|
| Activity         | MainActivity    | activity_main     |
| Fragment         | MainFragment    | fragment_main     |
| Dialog           | RateDialog      | dialog_rate       |
| Widget           | UserProfileView | view_user_profile |
| AdapterView Item | N/A             | item_follower     |

**Note:** If you create a layout using the merge tag then the layout_ prefix should be used.

## Best practices

#### Name your arguments

    
    // Bad
    someMethod(0, null, "Hey", new HashMap<Integer, String());

    // Good
    int index = 0;
    MyType xyz = null;
    String message = "Hey";
    Map<Intger, String> idMap = new HashMap<>();
    someMethod(index, xyz, message, idMap);

  - The reader does not have to switch contexts to figure out what types the arguments are.
  - The names give extra context to what the arguments represent
  - Adds extra type-safety (for null values)
  - It is more typing, but you type it once and read it many times.

#### Return early

    
    // Bad
    if (user != null) {
        user.doSomething();
        user.doMoreStuff();
        somethingElse();
        ...
    }

    // Good
    if (user == null) {
        return;
    }

    user.doSomething();
    user.doMoreStuff;
    somethingElse();

It gets rid of the indentation level and limits the chance of mistakes (accidentally adding code outside of the braces)

#### Keep code small

Classes, methods, interfaces -- they should all be small. There is no strong rule of thumb here, but strive to keep code seperated into small self-contained chunks of logic.

#### Keep code at the same level of abstraction

Within a method keep code at the same level of abstraction; otherwise you risk confusing a reader.

    
    // Bad
    private void openCaseWithId(final int id) {
        final String caseName = getCaseNameForId(id);
        final boolean isValidCase = caseName.startsWith("CASE0") == true;
        if (!isValidCase) {
            return;
        }

        final Case case = getCase(caseName);
        case.open();
    }

Here there is code at different levels of abstraction. There are low levels of abstraction (accessing a String directly), mid levels of abstraction (getting a case by id and name), and high levels of abstraction (opening a case).
Code should be rewritten to keep abstraction at the same levl.

    
    // Good
    private void openCaseWithId(final int id) {
        final Case case = getCaseWithId(id);
        if (case.isValid()) {
            case.open();
        }
    }

    // The string checking can be moved into Case::isValid();
    // Getting case by id and name has been merged into a single method

#### The best methods have no arguments, the rest have one.

This is a controversial title but aim to have methods with as few arguments as possible. A method with no arguments is easier to call than one with five arguments. Consider creating helper classes to reduce the number of arguments.

    
    // Bad
    public void drawArrow(int x, int y, int length) { ... }

    // Good
    public class Arrow {
         int x;
         int y;
         int length;
    }

    public void drawArrow(Arrow arrow);

#### Don't have methods with booleans as arguments

Methods that accept a boolean essentially do two things as as such should be two methods.

    
    // Bad
    public void enableView(boolean isEnabled) { ... }

    // Good
    public void enableView() { ... }
    public void disableView() { ... }

## Exception handling

#### Don't catch generic Exceptions

If you do so, you'll probably catch exceptions you don't want to catch and that should be thrown all the way up. Also, you should be aware of what exceptions the code you are calling can throw and you should have a plan to handle them.

    
    // Bad
    try {
        doSomething();
    } catch (Exception ex) {
        ...
    }

    // Good
    try {
        doSomething();
    } catch (NullPointerException ex) {
        ...
    }

#### Group Exceptions

    
    // Bad
    try {
        doSomething();
    } catch (NullPointerException ex) {
        Log.e(TAG, "error");
    } catch (ActivityNotFoundException ex) {
        Log.e(TAG, "error");
    }

    // Good
    try {
        doSomething();
    } catch (NullPointerException | ActivityNotFoundException ex) {
        Log.e(TAG, "error");
    }

#### Keep the catch close to the throwing code

    
    // Bad
    try {
        someCodeThatThrowsAnException();
        somethingElse();
        anotherThing();
        moreStuff();
        someMore();
    } catch (...) { ... }

    // Good
    try {
        methodThatWrapsAllOfTheAbove();
    } catch (...) { ... }

#### Don't throw an Exception

Throw a specific, maybe even custom Exception. That way you can convey information about what the issue is.
