# LanguageDetails: Inheritance and Access in Java

A practical lesson on packages, multilevel inheritance, and access modifiers in Java.

**Author:** Ushindi B. Victoire  
Software Q.A Engineer | Requirement Specialist | @ub-victoire | @ub-victor

## Learning objectives

By the end of this lesson, you should be able to:

- Organize Java classes into packages and import a class from another package.
- Explain how a subclass inherits accessible members from its superclass.
- Follow a multilevel inheritance chain across package boundaries.
- Distinguish `public`, `protected`, package-private, and `private` access.
- Compile and run a Java application using its fully qualified class name.
- Recognize when inheritance is useful and when another design may be better.

## The exercise

This project contains four classes, each in a different package:

| Package | Class | Method declared |
| --- | --- | --- |
| `org.german` | `German` | `germanLanguage()` |
| `org.english` | `English` | `englishLanguage()` |
| `org.hindi` | `Hindi` | `hindiLanguage()` |
| `org.all` | `LanguageClass` | `allLanguage()` and `main()` |

The goal is to create one `LanguageClass` object and call all four language-related methods on it. Three methods are inherited; `allLanguage()` is declared in `LanguageClass` itself.

The inheritance chain is:

```text
German  ->  English  ->  Hindi  ->  LanguageClass
```

Each arrow means **“is extended by.”** For example, `English extends German`, so `English` is a subclass of `German`. `German` is its superclass.

> **Modeling note:** English is not a kind of German, and Hindi is not a kind of English. This hierarchy is intentionally artificial: it teaches inheritance mechanics, not how to model languages in a real application.

## Project structure

```text
LanguageDetails/
├── Readme.md
├── .gitignore
└── src/
    └── org/
        ├── all/
        │   └── LanguageClass.java
        ├── english/
        │   └── English.java
        ├── german/
        │   └── German.java
        └── hindi/
            └── Hindi.java
```

`src` is the **source root**. The folders beneath it match the package names: for example, `org.hindi` corresponds to `src/org/hindi`.

Java is case-sensitive. The public class `LanguageClass` must be declared in a file named `LanguageClass.java`.

## Prerequisites

- A Java Development Kit (JDK) version 10 or later. The program uses `var`, which was introduced for local variables in Java 10.
- A terminal with `javac` and `java` available on the command path.
- Optionally, VS Code with the Extension Pack for Java.

Check your installation:

```bash
javac -version
java -version
```

No Maven, Gradle, or external libraries are required.

## Run the application

From the project root, compile all four source files:

```bash
javac -d out src/org/german/German.java src/org/english/English.java src/org/hindi/Hindi.java src/org/all/LanguageClass.java
```

The `-d out` option places the compiled `.class` files in `out`, organized into package directories. Then run the class containing `main`:

```bash
java -cp out org.all.LanguageClass
```

Here, `-cp out` puts the compiled output on the classpath, and `org.all.LanguageClass` is the class's **fully qualified name**: its package name followed by its class name.

Expected output:

```text
Languages in this demonstration: German, English, Hindi
German: Guten Tag!
English: Hello!
Hindi: Namaste!
```

In VS Code, open the **project root**, not an individual package folder. If the editor does not recognize the source root, create `.vscode/settings.json` in the project root:

```json
{
    "java.project.sourcePaths": ["src"]
}
```

Open `LanguageClass.java` and use the Run action above `main`. Workspace settings may be ignored by Git, so each reader may need to create this configuration locally.

## 1. Packages and imports

A package groups related classes and gives them a namespace. A source file normally begins with its package declaration:

```java
package org.english;
```

When a class needs to refer to a class in another package by its simple name, it can import it:

```java
import org.german.German;

public class English extends German {
    // ...
}
```

The import makes the simple name `German` available in this file. It does **not** make an inaccessible class or member accessible. Alternatively, the superclass could be written as `org.german.German` without the import.

Each subclass in this project imports its **direct superclass**. `LanguageClass` imports `Hindi`; it does not need to import `English` and `German` just to call methods inherited through the hierarchy.

Packages with a shared prefix are still distinct. For example, `org.german` and `org.english` are different packages; one is not granted access to the other's package-private members.

## 2. Build the inheritance chain

The following examples show the behavior of the project's source files, formatted for readability.

### Level 1: `German`

In `src/org/german/German.java`:

```java
package org.german;

public class German {
    public void germanLanguage() {
        System.out.println("German: Guten Tag!");
    }
}
```

`German` declares one public instance method. Since it does not explicitly extend another class, it implicitly extends `java.lang.Object`.

### Level 2: `English`

In `src/org/english/English.java`:

```java
package org.english;

import org.german.German;

public class English extends German {
    public void englishLanguage() {
        System.out.println("English: Hello!");
    }
}
```

`English` declares `englishLanguage()` and inherits the public `germanLanguage()` method from `German`.

### Level 3: `Hindi`

In `src/org/hindi/Hindi.java`:

```java
package org.hindi;

import org.english.English;

public class Hindi extends English {
    public void hindiLanguage() {
        System.out.println("Hindi: Namaste!");
    }
}
```

`Hindi` declares `hindiLanguage()` and inherits accessible methods from both `English` and `German`.

### Level 4: `LanguageClass`

In `src/org/all/LanguageClass.java`:

```java
package org.all;

import org.hindi.Hindi;

public class LanguageClass extends Hindi {
    public void allLanguage() {
        System.out.println("Languages in this demonstration: German, English, Hindi");
    }

    public static void main(String[] args) {
        var language = new LanguageClass();

        language.allLanguage();
        language.germanLanguage();
        language.englishLanguage();
        language.hindiLanguage();
    }
}
```

`main` is the application's entry point. The JVM invokes it when the class is launched. The statement `new LanguageClass()` creates one object; the local variable `language` refers to that object. The compiler infers its type as `LanguageClass`. The equivalent explicit declaration is:

```java
LanguageClass language = new LanguageClass();
```

### Where do the methods come from?

| Call on `language` | Method declared in | Relationship to `LanguageClass` |
| --- | --- | --- |
| `allLanguage()` | `LanguageClass` | Declared directly |
| `hindiLanguage()` | `Hindi` | Inherited from the direct superclass |
| `englishLanguage()` | `English` | Inherited through `Hindi` |
| `germanLanguage()` | `German` | Inherited through `English` and `Hindi` |

The method bodies are not copied into each subclass. Java resolves the calls using the class hierarchy. The output follows the **order of calls in `main`**, not the order of the classes in the inheritance chain.

These methods have different names, so this example does **not** override any of them. Overriding occurs when a subclass supplies its own implementation of an inherited method with a matching signature.

## 3. Access modifiers across packages

Access modifiers determine where classes and members can be used. The four classes and their language methods are `public` in this project.

| Member access | Same class | Other classes in the same package | Subclass in another package | Unrelated class in another package |
| --- | --- | --- | --- | --- |
| `public` | Yes | Yes | Yes | Yes |
| `protected` | Yes | Yes | Yes, subject to receiver rules | No |
| No modifier (package-private) | Yes | Yes | No | No |
| `private` | Yes | No | No | No |

This table describes access to **members**, assuming the containing class is itself accessible. Constructors and top-level classes have additional rules.

- **`public`:** usable from any package, provided the declaring class is accessible.
- **`protected`:** usable within the same package and from subclasses in other packages, subject to the receiver restriction described below.
- **Package-private:** declared with no access modifier. A package-private method in `German` is not inherited by `English`, because the classes are in different packages.
- **`private`:** accessible only within its declaring class; private members are not inherited by subclasses.

A top-level class can be `public` or package-private. For example, if `German` were declared without `public`, `English` in another package could not extend it—even if `germanLanguage()` were public. An import cannot override that restriction.

### The `protected` receiver rule

Outside the package where a protected instance member is declared, access must occur in a subclass context, and the receiver must have the accessing subclass's type or a subtype of it. Consider this separate example, where `germanLanguage()` is temporarily changed to `protected`:

```java
// Inside LanguageClass, which extends German indirectly:
LanguageClass language = new LanguageClass();
language.germanLanguage(); // Allowed
```

The call is inside `LanguageClass`, and the receiver has type `LanguageClass`. However, even inside `LanguageClass`, calling the method on a receiver declared as `German` would not satisfy the cross-package receiver rule:

```java
// Inside LanguageClass; assume org.german.German is imported:
German german = new German();
german.germanLanguage(); // Compile error: receiver is not a LanguageClass
```

This restriction applies to cross-package protected access; it does not apply in the same way to code in `org.german`.

## 4. Try the access-control experiment

Start with the working public version. Make one change at a time in `German.java`, save, and rerun the compile command from [Run the application](#run-the-application):

1. Change `public void germanLanguage()` to `protected void germanLanguage()`. The call in `LanguageClass.main` still compiles because it is made within a subclass on a `LanguageClass` receiver.
2. Remove the modifier entirely, producing `void germanLanguage()`. Compilation fails at the call in `LanguageClass`: the method is package-private to `org.german` and is not inherited across this chain.
3. Change it to `private void germanLanguage()`. Compilation also fails at that call because private methods are not inherited.
4. Restore `public`, then compile and run again.

These are intentional experiments, not changes required by the working project.

## 5. Inheritance, overriding, and design

Inheritance models an **is-a** relationship: a subclass is a more specialized form of its superclass. Java permits a class to directly extend one class; multilevel inheritance forms a chain of those single-superclass relationships.

This project uses inheritance to demonstrate how accessible methods travel through such a chain. It should not be used as a real taxonomy of languages. In a real application, a common `Language` interface implemented by separate `German`, `English`, and `Hindi` classes, or a collection of language objects, would usually express the domain more accurately.

Inheritance is also different from **overriding**. For example, in a separate design, multiple implementations could provide different versions of a `greet()` method. Calling `greet()` through a common type could then select an implementation according to the object's runtime type. This is polymorphism; the current project does not demonstrate overriding because its method names are distinct.

## Common problems

| Problem | Likely cause | What to check |
| --- | --- | --- |
| `The declared package ... does not match the expected package` | The editor is using the wrong source root. | Open the project root in VS Code and make sure `src` is configured as the source root. |
| `package ... does not exist` or an import cannot be resolved | A package declaration, directory, source root, or compile input is incorrect. | Compare package names with the directory layout and compile all four files. |
| `cannot find symbol` for a method or variable | A name may be misspelled or the member may be inaccessible. | Check spelling, capitalization, the inheritance chain, and access modifiers. |
| `javac: command not found` | The JDK is missing or not on the command path. | Install a JDK and configure the terminal environment, then check `javac -version`. |
| `Could not find or load main class` | The classpath or fully qualified name is wrong, or compilation did not succeed. | Run `java -cp out org.all.LanguageClass` from the project root after a successful compilation. |
| VS Code still shows errors after a correction | Java project diagnostics may need to refresh. | Save the files, verify the workspace root, and use **Java: Clean Java Language Server Workspace** if needed. |

## Practice questions

1. Why does `LanguageClass` import `Hindi` but not `German` or `English` to call the inherited methods?
2. What is the difference between a package declaration and an import?
3. What happens if `German` is package-private while `English` is in another package?
4. Why does the `protected` experiment work with a `LanguageClass` receiver but not necessarily with a `German` receiver?
5. Does this project override any language methods? Why or why not?
6. Why is the inheritance chain useful for the exercise but unsuitable as a real model of languages?

### Answer guide

1. `Hindi` is the direct superclass named in the declaration. Its inherited public methods are available through the hierarchy; the other class names do not appear in `LanguageClass`.
2. A package declaration states the package a source file belongs to. An import lets the file refer to an accessible type from another package using its simple name.
3. `English` cannot access or extend `German`, so compilation fails. A public method inside `German` does not make the class accessible.
4. The call is in a subclass outside `org.german`; for protected instance access, the receiver must have the accessing subclass's type or a subtype. `German` is a superclass, not a subtype of `LanguageClass`.
5. No. Each language method has a different name, and no subclass supplies a replacement implementation of an inherited method.
6. It shows multilevel inheritance clearly, but the classes do not represent genuine is-a relationships. Separate implementations of a common interface would be a more natural model.

## Further exercises

1. Add a `French` class in its own package and extend the chain. Create a `French` object and call the inherited methods. Explain why this remains an artificial model.
2. In a separate practice project, define a `Language` interface with a `greet()` method and implement it for two languages. Store both objects in a `Language` collection and call `greet()` on each.
3. Explain the difference between a method being **inherited** and being **accessible to an unrelated caller**. Use `protected` in your explanation.

## Summary

- Packages organize classes; imports help resolve names but do not grant access.
- `extends` creates a subclass-superclass relationship. Accessible methods can be inherited through several levels.
- Access modifiers control who can use a member, and cross-package `protected` access has additional receiver restrictions.
- Compile source files into an output directory, then run the fully qualified main class using that directory as the classpath.
- Choose inheritance for genuine is-a relationships, not merely to reuse methods.
