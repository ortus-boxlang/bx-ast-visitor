# BoxLang AST Visitor

A base visitor implementation for Boxlang parsing.  By creating an instance of a custom visitor class which extends our core class, you can "visit" a top level AST node, which will recursivley visit each node in the tree.  By overriding methods in your custom visitor, you can choose which nodes you want to "visit".  The convention is to create a method named `visit<NameOfNode>` where the name of the Node is the name of the Java class from the BoxLang AST.

https://github.com/ortus-boxlang/BoxLang/tree/development/src/main/java/ortus/boxlang/compiler/ast

## Void Visitor

A void visitor does not return anything from each visitXXX() method, but it can gather information about the nodes or mutate them in place.  

Example visitor classs
```java
class extends="bxmodules.bxASTVisitor.models.VoidBoxVisitor" {
	
	/**
	* visit each function declaration
	* https://github.com/ortus-boxlang/BoxLang/blob/development/src/main/java/ortus/boxlang/compiler/ast/statement/BoxFunctionDeclaration.java
	*/
	function visitBoxFunctionInvocation(node) {
		// Log out the name of the function 
		println( "Function Name: " & node.getName())
		// Must call this, or we won't recurse into children nodes
		visitChildren(node);
	}
	
}
```

Acquire the AST for some code and visit it like so:

```java
import ortus.boxlang.compiler.parser.Parser;
result = new Parser().parse( "echo( 'hello world' )" )
if( !result.getIssues().isEmpty() ) {
	println( result.getIssues() );
	throw( "Parsing failed" );
}
new MyVisitor().visit( result.getRoot() );
```

This will output
```
Function Name: echo
```
as the AST of the code will have a function invocation for echo().

Parse a file like so, using the AST in the same manner as above.
```java
import java.io.File;
import ortus.boxlang.compiler.parser.Parser;
result = new Parser().parse( new File( expandPath( 'MyClass.bx' ) ) )
```