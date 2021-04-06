# LLVM/Clang
## An analysis of interesting c++ features that have been used in LLVM/Clang codebase.

LLVM is a compiler infrastructure providing a collection reusable compiler and toolchain technologies.
Clang is an LLVM compiler for  C/C++/Objective-C source code, which aims to deliver amazingly fast compiles, extremely useful error and warning messages and to provide a platform for building great source level tools.

I have tried to read several .cpp and .h files from the clang [codebase](https://github.com/llvm/llvm-project/tree/main/clang) from llvm-project as well as doxygen [docs](https://clang.llvm.org/doxygen) which itself was an interesting read as i got introduced to [doxygen](https://www.doxygen.nl/index.html) a documentation generating tool and here are my findings:

## C++ features

+ ### auto
    C++ 11 provides auto keyword for declaring objects without specifying their types this reduces the verbosity of the code its used in thousands of places throughtout the codebase some of te instances are mentioned below:

    + In file [/clang/lib/Analysis/BodyFarm.cpp](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/lib/Analysis/BodyFarm.cpp):

        + [auto *FlagRecordDecl = FlagType->getAsRecordDecl();](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/lib/Analysis/BodyFarm.cpp#L340) here auto is used at the time of declaration

        + auto is particularly useful when iterating over an iterator object example [here](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/lib/Analysis/BodyFarm.cpp#L856)
        ``` 
        for (auto *Ext : OID->known_extensions()) 
        {
            auto *OMD = Ext->getInstanceMethod(D->getSelector());
                if (OMD && !OMD->isImplicit())
                return nullptr;
            } 
        ```
        The above example also demonstrate the usage of range for loop another new feature in c++11 where a list or container elements can be iterated in a very convinient and readable way
+ ### Inline-Functions

    C++ provides inline functions which provide a less expensive (no function call overhead) way of implementing a small function inline its used at many places in clang some of which are mentioned below:

    + In file [/clang/include/clang/Lex/Lexer.h](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/include/clang/Lex/Lexer.h)

        + [inline char getAndAdvanceChar(const char *&Ptr, Token &Tok)](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/include/clang/Lex/Lexer.h#L630)
        getAndAdvanceChar - Read a single 'character' from the specified buffer,advance over it, and return it.  This is tricky in several cases.  Here we just handle the trivial case and fall-back to the non-inlined getCharAndSizeSlow method to handle the hard case.
    

        + [inline char getCharAndSize(const char *Ptr, unsigned &Size)](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/include/clang/Lex/Lexer.h#L661)
        getCharAndSize - Peek a single 'character' from the specified buffer,get its size, and return it.  This is tricky in several cases.  Here we just handle the trivial case and fall-back to the non-inlined getCharAndSizeSlow method to handle the hard case.
        
        + [ static inline char getCharAndSizeNoWarn(const char *Ptr, unsigned &Size,const LangOptions &LangOpts)](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/include/clang/Lex/Lexer.h#L547)
            A static inline function in class clang::lexer

+ ### list initialization

    Since c++11 list (example vectors) variables can be initialized directly using braced list, example in clang:

     In file [/clang/include/clang/AST/NSAPI.h](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/include/clang/AST/NSAPI.h#L150)
     + [StringRef Ids[] = { "setObject", "forKeyedSubscript" };](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/include/clang/AST/NSAPI.h#L150)
     + [StringRef Ids[] = { "setObject", "atIndexedSubscript" };](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/include/clang/AST/NSAPI.h#L156)

+ ### lambda

    C++ 11 brought support for lambda functions thus bringing the power of lambda to c++.The structure of a lambda is defined with the following syntax in c++

    ```
    [ capture clause ] (parameters) -> return-type  
    {   
        definition
    } 
    ``` 
    Some instances of usage of lambdas in clang codebase are:

    + [[&](ValueInfo const &Info) { return Info.Kind == ValueKind; }](https://github.com/llvm/llvm-project/blob/main/clang/lib/AST/ComparisonCategories.cpp#L75)
    + [[&]{ return VisitorBase::TraverseDecl(DeclNode);}](https://github.com/llvm/llvm-project/blob/main/clang/lib/AST/ParentMapContext.cpp#L417)
    + [[&]{ return VisitorBase::TraverseTypeLoc(TypeLocNode); }](https://github.com/llvm/llvm-project/blob/main/clang/lib/AST/ParentMapContext.cpp#L423)
    + [[&]{ return VisitorBase::TraverseNestedNameSpecifierLoc(NNSLocNode);}](https://github.com/llvm/llvm-project/blob/main/clang/lib/AST/ParentMapContext.cpp#L429)

+ ### nullptr
    nullptr was introduced in c++ 11 its a pure rvalue (prvalue) its a pointer literal of type std::nullptr_t , its a universal null pointer replacing literal 0 and macro NULL.
    LLVM codebase uses nullptr at numerous places some instances are mentioned below:

    In file [/clang/lib/Analysis/CallGraph.cpp](https://github.com/llvm/llvm-project/blob/main/clang/lib/Analysis/CallGraph.cpp)

    + [return nullptr;](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/lib/Analysis/CallGraph.cpp#L66) (returned from function getDeclFromCall which returns Decl*)
    
    + [Decl *D = nullptr;](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/lib/Analysis/CallGraph.cpp#L119) (assigned to D of type Decl*)
    
    + [ Root = getOrInsertNode(nullptr);](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/lib/Analysis/CallGraph.cpp#L150)
    (passed to function CallGraphNode *CallGraph::getOrInsertNode(Decl *F))
    
    + [Root->addCallee({Node.get(), /*Call=*/nullptr});](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/lib/Analysis/CallGraph.cpp#L214) (passed to constructor of struct CallGraphNode::CallRecord ,CallRecord(CallGraphNode *Callee_, Expr *CallExpr_))

## Generic Programming

Generic programming is a technique of increasing code effciency by avoiding re writing of code for handling different data types this is achieved by using templates , in c++ functions , classes ,structs and in c++ 11 variables also can be templated. Some instances of usage of templates in clang codebase are mentioned below:

+ ### Template functions

In file [/clang/lib/Analysis/CFG.cpp](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/lib/Analysis/CFG.cpp)

```
template <typename CallLikeExpr,
            typename = std::enable_if_t<
                std::is_base_of<CallExpr, CallLikeExpr>::value ||
                std::is_base_of<CXXConstructExpr, CallLikeExpr>::value ||
                std::is_base_of<ObjCMessageExpr, CallLikeExpr>::value>>
```
                
[void findConstructionContextsForArguments(CallLikeExpr *E)](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/lib/Analysis/CFG.cpp#L720)

Scan all arguments of a call expression for a construction context.These sorts of call expressions don't have a common superclass, hence strict duck-typing.

+ ### Template Class/Struct
 In file [/clang/include/clang/AST/TypeVisitor.h](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/include/clang/AST/TypeVisitor.h)
    
    template<typename ImplClass, typename RetTy=void>

[class TypeVisitor](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/include/clang/AST/TypeVisitor.h#L64)

    
## Class-Hierarchy

Class hierarchy is decided by the inheritance of classes
Some of the classes which i studied in the codebase had following hierarchies:

+ In file [/clang/include/clang/Analysis/CallGraph.h](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/include/clang/Analysis/CallGraph.h)

    + [class CallGraph : public RecursiveASTVisitor<CallGraph>](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/include/clang/Analysis/CallGraph.h#L42)

        CallGraph inherits RecursiveASTVisitor<CallGraph> which reorder or postorder
depth-first traversal on the entire Clang AST and visits each node.

+ In file [/clang/include/clang/Lex/Lexer.h](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/include/clang/Lex/Lexer.h)

    + [class Lexer : public PreprocessorLexer](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/include/clang/Lex/Lexer.h#L76)

        Lexer inherits clang::Preprocessor

Class hierarchy can be further studied in the doxygen docs [here](https://clang.llvm.org/doxygen/inherits.html)

## OOPS

+ ### Inheritance
    The act of inheriting methods and attributes of a class by another class is called inheritance the class which inherits is called derived class and the class which is inherited is called base class.

    Inheritance is a very powerful oops concept for defining a relationship between classes as well as reusability of code. In large codebases for maintaining the understandibility of the code such reusability becomes very necessary so its used vastly throughout clang's codebase some instances are mentioned in the [above section](#Class-Hierarchy) which gives some examples of class heirarchies


+ ### Encapsulation
    Encapsulation is the act of binding data members and methods in a single unit this is used in almost every class ,we can implement data abstraction by using various access specifiers ie public private protected:

    Access Specifier  | Visible to
    ------------- | -------------
    public   | Everywhere
    private  | class where declared
    protected  | class where declared+inheriting classes+friend classes

    These access specifiers are aptly used in class designs in various header files in clang codebase.


+ ### Polymorphism
    C++ supports polymorphism its act of providing same interface with different functionalities Ex:when derived classes need to implement a function in different manner, interface provides virtual functions.
    
    Polymorphism is of 2 kinds :

    + Compile time: can be achieved by function or operator overloading
    + Runtime : can be achieved by using virtual functions

    + An example of function overloading

        + [Bodyfarm](https://clang.llvm.org/doxygen/classclang_1_1BodyFarm.html) in file [/clang/lib/Analysis/BodyFarm.cpp](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/lib/Analysis/BodyFarm.cpp):
        
            + [Stmt*BodyFarm::getBody(const FunctionDecl * D)](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/lib/Analysis/BodyFarm.cpp#L671)	 

                Factory method for creating bodies for ordinary functions.
            + [Stmt * BodyFarm::getBody	(const ObjCMethodDecl * D)](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/lib/Analysis/BodyFarm.cpp#L823)

                Factory method for creating bodies for Objective-C properties.


+ ### Interfaces 
    In c++ to make a class an abstract class/Interface a class must include at least one pure virtual function

    + In file [/clang/include/clang/Lex/PreprocessorLexer.h](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/include/clang/Lex/PreprocessorLexer.h)

    + ### Pure virtual functions
        + [virtual void IndirectLex(Token& Result) = 0](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/include/clang/Lex/PreprocessorLexer.h#L79)
        + [virtual SourceLocation getSourceLocation() = 0](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/include/clang/Lex/PreprocessorLexer.h#L84)
    
## Iterators

In c++ [iterators](https://www.cplusplus.com/reference/iterator/) are abject pointing to elements in an array or container(vector,list,map etc), some instances of usage of iterators in clang codebase are mentioned below:

These functions returns the iterators returned from begin() and end() members
+ [iterator begin() { return FunctionMap.begin();}](https://github.com/llvm/llvm-project/blob/main/clang/include/clang/Analysis/CallGraph.h#L86)
+ [iterator end() { return FunctionMap.end();}](https://github.com/llvm/llvm-project/blob/main/clang/include/clang/Analysis/CallGraph.h#L87)

Iterators are primarily used in for loops for iterating over a container.

## Design Patterns

+ ### Factory Pattern
    The factory method pattern is a creational pattern that uses factory methods to deal with the problem of creating objects without having to specify the exact class of the object that will be created. This is done by creating objects by calling a factory method—either specified in an interface and implemented by child classes, or implemented in a base class and optionally overridden by derived classes—rather than by calling a constructor.

    The factory pattern is used numerous times in clang codebase some of these instances are shown below:


    + [Bodyfarm](https://clang.llvm.org/doxygen/classclang_1_1BodyFarm.html) in file [/clang/lib/Analysis/BodyFarm.cpp](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/lib/Analysis/BodyFarm.cpp):
    
        + [Stmt*BodyFarm::getBody(const FunctionDecl * D)](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/lib/Analysis/BodyFarm.cpp#L671)	 

            Factory method for creating bodies for ordinary functions.
        + [Stmt * BodyFarm::getBody	(const ObjCMethodDecl * D)](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/lib/Analysis/BodyFarm.cpp#L823)

            Factory method for creating bodies for Objective-C properties.

    + [Parser.h]in file [/clang/include/clang/Parse/Parser.h](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/include/clang/Parse/Parser.h):
        + A factory, from which one makes pools, from which one creates individual attributes which are deallocated with the pool.[here](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/include/clang/Parse/Parser.h#L277)
        ```
        /// Factory object for creating ParsedAttr objects.
        AttributeFactory AttrFactory;
        ```

+ ### Curiously Recurring Template Pattern (CRTP)
    The curiously recurring template pattern (CRTP) is an idiom in C++ in which a class X derives from a class template instantiation using X itself as a template argument.

    + [TypeVisitor](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/include/clang/AST/TypeVisitor.h)

## References

+ https://llvm.org/
+ https://clang.llvm.org/doxygen
+ https://en.wikipedia.org/wiki/Factory_method_pattern
+ https://en.wikipedia.org/wiki/Curiously_recurring_template_pattern
+ https://clang.llvm.org/doxygen/inherits.html
+ https://docs.microsoft.com/en-us/cpp/cpp/lambda-expressions-in-cpp?view=msvc-160#:~:text=A%20lambda%20can%20introduce%20new,by%20value%20or%20by%20reference.
+ https://docs.microsoft.com/en-us/cpp/standard-library/iterators?view=msvc-160



## Author
## [Kushagra Indurkhya](https://github.com/KushagraIndurkhya)
## CS19B1017


