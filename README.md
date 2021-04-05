# LLVM/Clang
An analysis of interesting features that have been used in LLVM/Clang codebase.

## C++ features

+ ### auto

+ ### Inline-Functions

    C++ provides inline functions which provide a less expensive (no function call overhead) way of implementing a small function inline its used at many places in clang some of which are mentioned below:

    + In file [/clang/include/clang/Lex/Lexer.h](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/include/clang/Lex/Lexer.h)

        + [inline char getAndAdvanceChar(const char *&Ptr, Token &Tok)](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/include/clang/Lex/Lexer.h#L630)
        getAndAdvanceChar - Read a single 'character' from the specified buffer,advance over it, and return it.  This is tricky in several cases.  Here we just handle the trivial case and fall-back to the non-inlined getCharAndSizeSlow method to handle the hard case.
    

        + [inline char getCharAndSize(const char *Ptr, unsigned &Size)](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/include/clang/Lex/Lexer.h#L661)
        getCharAndSize - Peek a single 'character' from the specified buffer,get its size, and return it.  This is tricky in several cases.  Here we just handle the trivial case and fall-back to the non-inlined getCharAndSizeSlow method to handle the hard case.
        
        + [ static inline char getCharAndSizeNoWarn(const char *Ptr, unsigned &Size,const LangOptions &LangOpts)](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/include/clang/Lex/Lexer.h#L547)
            A static inline function in class clang::lexer

+ ### lambda

<!-- + ### range based loops -->
+ ### nullptr
    nullptr was introduced in c++ 11 its a pure rvalue (prvalue) its a pointer literal of type std::nullptr_t , its a universal null pointer replacing literal 0 and macro NULL.
    LLVM codebase uses nullptr at numerous places some instances are mentioned below:

    In file [/clang/lib/Analysis/CallGraph.cpp](https://github.com/llvm/llvm-project/blob/main/clang/lib/Analysis/CallGraph.cpp)

    + [return nullptr;](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/lib/Analysis/CallGraph.cpp#L66) (returned from function getDeclFromCall which returns Decl*)
    
    + [Decl *D = nullptr;](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/lib/Analysis/CallGraph.cpp#L119) (assigned to D of type Decl*)
    
    + [ Root = getOrInsertNode(nullptr);](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/lib/Analysis/CallGraph.cpp#L150)
    (passed to function CallGraphNode *CallGraph::getOrInsertNode(Decl *F))
    
    + [Root->addCallee({Node.get(), /*Call=*/nullptr});](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/lib/Analysis/CallGraph.cpp#L214) (passed to constructor of struct CallGraphNode::CallRecord ,CallRecord(CallGraphNode *Callee_, Expr *CallExpr_))

+ ### 

## Generic Programming
+ ### Template functions

In file [/clang/lib/Analysis/CFG.cpp](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/lib/Analysis/CFG.cpp)

```template <typename CallLikeExpr,
            typename = std::enable_if_t<
                std::is_base_of<CallExpr, CallLikeExpr>::value ||
                std::is_base_of<CXXConstructExpr, CallLikeExpr>::value ||
                std::is_base_of<ObjCMessageExpr, CallLikeExpr>::value>>
```
                
[void findConstructionContextsForArguments(CallLikeExpr *E)](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/lib/Analysis/CFG.cpp#720)

Scan all arguments of a call expression for a construction context.These sorts of call expressions don't have a common superclass, hence strict duck-typing.

+ ### Template Class/Struct
 In file [/clang/include/clang/AST/TypeVisitor.h](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/include/clang/AST/TypeVisitor.h)
    
    template<typename ImplClass, typename RetTy=void>

[class TypeVisitor](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/include/clang/AST/TypeVisitor.h#L64)

    
## Class Hierarchy

Some of the classes which i studied in the codebase had following hierarchies:

+ In file [/clang/include/clang/Analysis/CallGraph.h](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/include/clang/Analysis/CallGraph.h)

    + [class CallGraph : public RecursiveASTVisitor<CallGraph>](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/include/clang/Analysis/CallGraph.h#L42)

        CallGraph inherits RecursiveASTVisitor<CallGraph> which reorder or postorder
depth-first traversal on the entire Clang AST and visits each node.

+ In file [/clang/include/clang/Lex/Lexer.h](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/include/clang/Lex/Lexer.h)

    + [class Lexer : public PreprocessorLexer](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/include/clang/Lex/Lexer.h#L76)

        Lexer inherits clang::Preprocessor
    
+ In file [/clang/include/clang/Lex/Lexer.h](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/include/clang/Lex/Lexer.h)


## OOPS

+ ### Inheritance

+ ### Encapsulation

+ ### Polymorphism

+ ### Interfaces 
    In c++ to make a class an abstract class/Interface a class must include at least one pure virtual function

    + In file [/clang/include/clang/Lex/PreprocessorLexer.h](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/include/clang/Lex/PreprocessorLexer.h)

    + ### Pure virtual functions
        + [virtual void IndirectLex(Token& Result) = 0](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/include/clang/Lex/PreprocessorLexer.h#79)
        + [virtual SourceLocation getSourceLocation() = 0](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/include/clang/Lex/PreprocessorLexer.h#84)
    


## Design Patterns

+ ### Factory Pattern
    The factory method pattern is a creational pattern that uses factory methods to deal with the problem of creating objects without having to specify the exact class of the object that will be created. This is done by creating objects by calling a factory method—either specified in an interface and implemented by child classes, or implemented in a base class and optionally overridden by derived classes—rather than by calling a constructor.

    The factory pattern is used numerous times in clang codebase some of these instances are shown below:

    + [Bodyfarm](https://clang.llvm.org/doxygen/classclang_1_1BodyFarm.html) -
    + []()

+ ### Curiously Recurring Template Pattern (CRTP)
    The curiously recurring template pattern (CRTP) is an idiom in C++ in which a class X derives from a class template instantiation using X itself as a template argument.
    + [TypeVisitor](https://github.com/llvm/llvm-project/blob/ea069aebccd317f350be3cabdcd848476616d4da/clang/include/clang/AST/TypeVisitor.h)



### References

+ https://clang.llvm.org/doxygen
+ https://en.wikipedia.org/wiki/Factory_method_pattern
+ https://en.wikipedia.org/wiki/Curiously_recurring_template_pattern
+ https://clang.llvm.org/doxygen/inherits.html


