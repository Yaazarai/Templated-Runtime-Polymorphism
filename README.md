# Templated-Runtime-Polymorphism
Demonstration of how templates can be used for datatype runtime polymorphism.

Here we create a base class `base` and a class to inherent `base` as it's parent called `deriviedT`. `derivedT` however is also templated, so it uses compile time polymorphism via templates. From here we create a function call in the `base` and we create it's equivalent in the `derivedT` and we mark them both as virtual.

    class base {
        public:
        virtual double call(double xx) {
            cout << "DERIVED: " << xx << endl;
            return xx;
        }
    };

    template<typename T>
    class derivedT : public base {
        public:
        virtual T call(double xx) {
            cout << "DERIVED_T: " << ((T) xx) << endl;
            return (T) xx;
        }
    };

The constraint here however is that the two virtual functions MUST have the same types parameters and return types in order for the `deriveT` class to override the functions in the `base` class. In otherwords the methods must have the same `signature`. If the method singature is `void call(char*)` in the `base` class then the method signature for `derivedT` must match `void call(char*)`. What does this mean? Well it means we can't send data to the class `derivedT` as type `T`. However it is possible to get around this by casting whatever data we send through the method to `T` (see above example).

Now let's demonstrate by creating an example:
```C+++
base* = new deriveT<int>();
base->call();
```
The result here will be the method execution for `deriveT` not `base` because the inheritee virtual method overrides the inherited virtual method.

Sooo... why does this work? Well because templates are compile time they cannot be generated at runtime. So instead we allow the compiler to generate templates for all types that we define in our code. We define all instances of `derivedT` as derivative of `base` and then we can access each template without having to specify the type. This is pretty neat, because now you can write code that is not dependant upon types and continue on without worry. For example if you want you could now define a `std::vector` that can support multiple subtypes without rew-writing the whole vector library, simply by define the vector as `std::vector<base>`.

For example if we wanted to pick a type at runtime we could use user input to choose a type from a switch statement.
```C++
int main() {
    base* sample = nullptr;

    std::cout << "CHOOSE TYPE: (BYTE = 1, UINT = 2, DOUBLE = 3)" << std::endl;
    uint32_t type;
    std::cin >> type;
    
    switch (type) {
        case 1:
            sample = static_cast<base*>(new derivedT<uint8_t>());
        break;
        case 2:
            sample = static_cast<base*>(new derivedT<int32_t>());
        break;
        case 3:
            sample = static_cast<base*>(new derivedT<float_t>());
        break;
    }

    sample->call(2567.45);
    system("pause");
}
```

NOTE: The only `#includ` used here was `iostream`.
