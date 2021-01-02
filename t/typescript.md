# TypeScript

## Notes

* Typed superset of JavaScript developed by Microsoft
* It compiles to readable JavaScript
* The type system disappears during the build step, so there is no runtime penalty
* By moving some errors from runtime to compile time, it becomes easier and faster to fix them

### Configuring TypeScript

* Flags to the `tsc` command

```bash
tsc src/index.ts --target ES2017 --module commonjs --watch
```

* `tsconfig.json` file

```javascript
{
    "compilerOptions": {
        "jsx": "react", // transform TSX files into JSX
        "strict": true, // enable strict features
        "module": "commonjs",
        "allowJs": true, // check and compile JS
        "target": "es2017", // target environment
        "outDir": "lib",
        "declaration": true, // create *.d.ts files
        "sourceMap": true, // create source map for debug
    },
    "include": ["src"], // input files
}
```

### Basics

```javascript
let x = "hello world"; // x is of type string
x = "hello mars"; // works OK
x = 42; // ERROR - number is not assignable to string

const y = "hello world"; // type is "hello world"
```

```javascript
let z; // not initialized, type is any (top type)
z = 41; // allowed because number is subtype of any
z = "abc"; // same as above

let zz: number; // explicit type annotation
zz = 41;
zz = "abc"; // ERROR - string is not assignable to numeber
```

```javascript
 let aa: number[] = []; // aa is of type array of number
 aa.push(33); // works OK
 aa.push("abc"); // ERROR - cannot add a string into a number array
```

```javascript
let bb: [number, string, string, number] = [
    123,
    "Fake Street",
    "Nowhere, USA",
    10110
]; // bb is a tuple - array of fixed length

bb = [1, 2, 3]; // ERROR - second and third arguments are string
```

```javascript
let cc: { houseNumber: number; streetName: string };
cc = {
    streetName: "Fake Street",
    houseNumber: 123
};

cc = {
    houseNumber: 33
}; // ERROR - all members are required by default

let dd: { houseNumber: number; streetName?: string };
dd = {
    houseNumber: 33
}; // OK - streetName? is now optional

interface Address {
    houseNumber: number;
    streetName?: string;
}
let ee: Address = { houseNumber: 33 }; 
```

```javascript
export interface HasPhoneNumber {
    name: string;
    phone: number;
}

export interface HasEmail {
    name: string;
    email: string;
}

let contactInfo: HasEmail | HasPhoneNumner; // intersection type
contactInfo.name = "Mike"; // we can access only name, because it's in common between the interfaces

let otherContactInfo: HasEmail & HasPhoneNumber = {
    name: "Mike",
    email: "mike@example.com",
    phone: 3215551212
}; // union type - must be initialized with all fields
otherContactInfo.name;
otherContactInfo.email;
otherContactInfo.phone; // we can access all
```

### Functions

```javascript
// Classical functions
function sendEmail(to: HasEmail): { recipient: string; body: string } {
    return {
        recipient: `${to.name} <${to.email}>`
        body: "You're pre-qualified for a loan!"
    };
}

// Arrow-head notation
const sendTextMessage = (
    to: HasPhoneNumber
): { recipient: string; body: string } => {
    return {
        recipient: `${to.name} <${to.phone}>`
        body: "You're pre-qualified for a loan!"
    };
};
```

```javascript
// overload signatures
function contactPeople(method: "email", ...people: HasEmail[]): void;
function contactPeople(method: "phone", ...people: HasPhoneNumber[]): void;

// function implementation
function contactPeople(
    method: "email" | "phone",
    ...people: (HasEmail | HasPhoneNumber)[]
): void {
    if (method === "email") {
        (people as HasEmail[]).forEarch(sendEmail);
    } else {
        (people as HasPhoneNumber[]).forEach(sendTextMessage);
    }
} // overload signatures prevent cases of method "email" but people of type HasPhoneNumber
```

### Interfaces and Type Aliases

```javascript
type StringOrNumber = string | number;
type HasName = { name: string };
```

```javascript
interface HasInternationalPhoneNumber extends HasPhoneNumber {
    countryCode: string;
}
```

```javascript
interface ContactMessenger1 {
    (contact: HasEmail | HasPhoneNumber, message: string): void;
}

type ContactMessenger2 = (
    contact: HasEmail | HasPhoneNumber,
    message: string
) => void;

const emailer: ContactMessenger1 = (_contact, _message) => {
    /* ... */
}
```

```javascript
interface PhoneNumberDict {
    [numberName: string]:
        | undefined
        | {
            areaCode: number;
            num: number;
          };
}

const d: PhoneNumberDict = {
    abc: { areaCode: 837; num: 9283123 }
}

d.jkl // undefined
if (d.abc) { // d.abc is of type undefined | { areaCode: number; num: number }
    d.abc // d.abc is of type { areaCode: number; num: number }
}
```

### Classes

```javascript
class Contact implements HasEmail {
    email: string;
    name: string;
    constructor(name: string, email: string) {
        this.name = name;
        this.email = email;
    }
}

class ParamPropContact implements HasEmail {
    constructor(
        public name: string,
        public email: string = "no email"
    ) {
        // nothing needed
    }
}

class OtherContact implements HasEmail, HasPhoneNumber {
    protected age: number = 0;
    
    constructor(
        public name: string,
        public email: string,
        public phone: number) {
            this.age = 35;
    }
}
```

```javascript
abstract class AbstractContact implements HasEmail, HasPhoneNumber {
    public abstract phone: number;
    
    constructor (
        public name: string,
        public email: string
    ) {}
    
    abstract sendEmail(): void;
}

class ConcreteConteact extends AbstractContact {
    constructor(
        public phone: number,
        name: string,
        email: string
    ) {
        super(name, email);
    }
    
    sendEmail() {
        console.log("sending an email");
    }
}
```

### Converting from JavaScript to TypeScript

1. Compile project in "loose mode"
   1. start with tests passing
   2. rename all files to `.ts`, with implicit any
   3. fix only compile errors \(do not change behaviour\)
   4. get tests passing again
2. Explicit Any
   1. start with tests passing
   2. add `"noImplicitAny": true` to `tsconfig.json`
   3. Where possible, provide a specific and appropriate type \(explicit any otherwise\)
   4. get tests passing again
3. Squash explicit anys, enable strict mode
   1. Enable strict mode in `tsconfig.json`
   2. Replace explicit anys with more appropriate types \(incrementally, in small chunks\)

### Generics

```javascript
interface WrappedValue<X> {
    value: X;
}

let val: WrappedValue<string> = { value: '' };
val.value; // value is of type string
```

```javascript
interface FilterFunction<T = any> {
    (val: T): boolean
}

const stringFilter: FilterFunction<string> = val => typeof val === "string";
stringFilter(0); // ERROR - 0 is not a string
stringFilter("abc"); // OK

const truthyFilter: FilterFunction = val => val
truthyFilter(0); // false - valid because T is any because there was no type
truthyFilter(1); // true - valid because T is any because there was no type
```

```javascript
function arrayToDict<T extends { id: string }>(array: T[]): { [k: string]: T} {
    const out: { [k: string]: T } = {};
    arrayForEach(val => {
        out[val.id] = val; // without extends {id: string} this line will not work
    });
    return out;
}
```

### Top and Bottom types

```javascript
let myAny: any = 32; // all values can be assigned to any
let myUnknown: unknown = "hello, unknown"; // all value can be assigned to unknown

myAny.foo.bar.baz; // valid
myUnknown.foo; // ERROR!
if (typeof myUnknown === "string") {
    myUnknown.split(", "); // OK
}

// user-defined type guards
function isHasEmail(x: any): x is HasEmail {
    return typeof x.name === "string" && typeof x.email === "string";
}
if (isHasEmail(myUnknown)) {
    // in here, myUnknown is of type HasEmail
    console.log(myUnknown.name, myUnknown.email);
}
```

```javascript
let n: never = 4; // all values can be assigned to never

let x = "abc" as string | number;

if (typeof x === "string") {
    // x is a string here
    x.split(", ");
} else if (typeof x === "number") {
    // x is a number here
    x.toFixed(2);
} else {
    // x is a never here
}
```

### Advanced Types

```javascript
interface CommunicationMethods {
    email: HasEmail;
    phone: HasPhoneNumber;
    fax: { fax: number };
}

function contact<K extends keyof CommunnicationMethods>(
method: K,
contact: CommunicationMethods[K]
) {
    // ...
}

type AllCommsKeys = keyof CommunicationMethods
type AllCommValues = CommunicationMethods[keyof CommunicationMethods]
```

```javascript
type MayHaveEmail = Partial<HasEmail>;
const me: MayHaveEmail = {}; // everything is optional
```

```javascript
type PickA = Pick<{a: 1, b: 2}, "a"};
const y: PickA;
y.a // valid
y.b // ERROR! 
```

## Resources

### Books

* [TypeScript Deep Dive](https://basarat.gitbook.io/typescript/) - Basarat Ali Syed

### Courses

* [TypeScript 3 Fundamentals v2](https://frontendmasters.com/courses/typescript-v2/) - Mike North

### Websites

* [DefinitelyTyped](https://definitelytyped.org/) - The repository for high quality TypeScript type definitions
* [r/typescript](https://www.reddit.com/r/typescript/) - TypeScript subreddit
* [TypeScript](https://www.typescriptlang.org/) - Official website and docs



