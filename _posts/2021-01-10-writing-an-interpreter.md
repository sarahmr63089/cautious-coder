---
layout: post
title: Writing an Interpreter Part 1
date: 2021-01-10 15:41:38 -0400
author: Sarah
image: assets/code_computer_development_programming.png
excerpt: I walk through building a single character scanner with code samples.
---

The longer I've spent coding, the more interested I've become in how languages work. Through that research I came across the book Crafting Interpreters by Bob Nystrom. The book walks the reader through creating two language intepreters for a language called Lox. The first interpreter is presented in Java and the second in C.  For my next series of blog posts, I'm going to walk through what I'm learning as I follow his book through the first interpreter which I'm writing in Go.

Before I start with the code, I'd like to briefly define compilers and interpreters. One of the differences between languages is if they're compiled or interpreted. Go, C, and C++ are compiled languages meaning that they use a compiler to convert the program into binary code before the code is executed. Interpreted languages include Ruby and Python, and interpreters execute the code without converting them to machine code first. 

The first part of the process is scanning or lexical analysis. This is where the interpreter (or compiler) takes in the source code and looks for tokens. These tokens are chunks that the program has learned to recognize. For the first part of my scanner, I focused on single-character tokens, such as ",", ".", and "[".

In Go, programs are run through the main package, so we'll start with the set up there:

{% highlight go %}

func main() {
	args := os.Args[1:]

	if len(args) > 1 {
		fmt.Println("Usage: lox [script]")
		os.Exit(64)
	} else if len(args) == 1 {
		runFile(args[0])
	}
}

func runFile(path string) {
	file, err := ioutil.ReadFile(path)

	if err != nil {
		panic(err)
	}
	// fmt.Print(string(file))
	run(string(file))
}

func run(source string) {
	loxscanner := scanner.NewScanner(source)

	tokens := loxscanner.ScanTokens()

	for _, token := range tokens {
		fmt.Println(token)
	}
}

{% endhighlight %}

The main function looks at the supplied argument and if the argument passes the second condition, it runs the runFile function, which will run the run function.

In the run function the source string is used to create a new Scanner struct and after running the source string through the ScanTokens functions, the tokens are printed out.

The real meat of the program so far lies in the scanner package. That's where the scanner and token structs are defined and where the scanning happens.

In the scanner package, I first declared a new type TokenType and set up some single character tokens as constants.

{% highlight go %}

type TokenType string

const (
	// single-character tokens
	LeftParen  TokenType = "LeftParen"
	RightParen TokenType = "RightParen"
	LeftBrace  TokenType = "LeftBrace"
	RightBrace TokenType = "RightBrace"
	Comma      TokenType = "Comma"
	Dot        TokenType = "Dot"
	Minus      TokenType = "Minus"
	Plus       TokenType = "Plus"
	SemiColon  TokenType = "SemiColon"
	Slash      TokenType = "Slash"
	Star       TokenType = "Star"

	EOF TokenType = "EOF"
)

{% endhighlight %}

From here, I set up the Token and Scanner structs:

{% highlight go %}

type Token struct {
	Type    TokenType
	Lexeme  string
	Literal Literal
	Line    int
}

func (t Token) ToString() string {
	return string(t.Type) + " " + t.Lexeme + " " + t.Literal.ToString()
}

type Scanner struct {
	Source  string
	Tokens  []Token
	start   int
	current int
	line    int
}

{% endhighlight %}

With that in place, it's ready for the NewScanner and ScanTokens functions.

The NewScanner function is pretty straightforward. It takes a source and creates a Scanner struct that begins at line 1. While my interpreter doesn't yet implement the line count part of the Scanner, it will eventually be able to tell us where to find the tokens.

{% highlight go %}

func NewScanner(source string) Scanner {
	return Scanner{
		Source: source,
		line:   1,
	}
}

{% endhighlight %}

The ScanTokens function has a few associated helper functions: isAtEnd, scanTokens, advance, and addToken. ScanTokens returns an array of tokens for the scanner that called it. 

{% highlight go %}

func (s *Scanner) ScanTokens() []Token {

	for !s.isAtEnd() {
		s.start = s.current
		s.scanToken()
	}

	s.Tokens = append(s.Tokens, Token{
		Type: EOF,
		Line: s.line,
	})
	return s.Tokens
}

{% endhighlight %}

ScanTokens passes the scanner source through scanToken and adds found Tokens to the scanner's list of Tokens until the scanner's current is greater than or equal to the length of the source.

{% highlight go %}

func (s Scanner) isAtEnd() bool {
	return s.current >= len(s.Source)
}

func (s *Scanner) scanToken() {
	c := s.advance()

	switch c {
	case "(":
		s.addToken(LeftParen)
	case ")":
		s.addToken(RightParen)
	case "{":
		s.addToken(LeftBrace)
	case "}":
		s.addToken(RightBrace)
	case ",":
		s.addToken(Comma)
	case ".":
		s.addToken(Dot)
	case "-":
		s.addToken(Minus)
	case "+":
		s.addToken(Plus)
	case ";":
		s.addToken(SemiColon)
	case "*":
		s.addToken(Star)
	}
}

{% endhighlight %}

The last two helper functions, advance and addToken, are called by scanTokens. Advance moves the scanner's current attribute along the source and addToken adds a token to the scanner's list of tokens with the appropriate attributes.  

{% highlight go %}

func (s *Scanner) advance() string {
	s.current++
	return string(s.Source[s.current-1])
}

func (s *Scanner) addToken(t TokenType) {
	text := s.Source[s.start:s.current]

	s.Tokens = append(s.Tokens, Token{
		Type:   t,
		Lexeme: text,
		Line:   s.line,
	})
}

{% endhighlight %}

This is the extent of my interpreter so far! I'm learning a lot through this process about how languages are read and about Go, which is a new language for me. I'm still wrapping my head around a lot of it, but re-reading and explaining the code here is helping a lot. 

In the next part of my interpreter I will be working with longer tokens, operators, numbers, and reserved words! Thanks for coming along on the journey!

Resources:

[Crafting Interpreters](http://craftinginterpreters.com)  
[Geeks for Geeks; Compiler vs Interpreter](https://www.geeksforgeeks.org/compiler-vs-interpreter-2/)  
Feed Image by Opensource.com