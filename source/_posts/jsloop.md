---
title: Self-replicating Code (in javascript)
tags:
  - Self-reference
  - Javascript
  - Recursion
date: 2016-05-02 15:06:20
index_img: /img/jsloop.gif
excerpt: Self-referencing code (Quine) is a program that can print its own source code when run. There are many C language and C# versions online, and I'm going to write a JavaScript version.
---

　　Self-replicating code (Quine) is a program that can print its own source code when run. There are many C language and C# versions online, and I'm going to write a JavaScript version.
## Digression: eval function/infinite recursion
　　JavaScript is an interpreted language, and it has a powerful function `eval()`: it can execute the received string as JavaScript code. I came up with this piece of code:
```javascript
s="eval(s)";eval(s);
```

　　We can do this experiment in any browser: just type `javascript:s="eval(s)";eval(s);` into the address bar. (Go and try it..)
　　After running it in the Chrome console for a while, you get this error: \<span style="color:\#F00"\>Uncaught RangeError: Maximum call stack size exceeded\</span\>\<span style="color:\#999\>(…)\</span\>

\<hr/\>
　　Here's another interesting infinite recursion experiment: execute these statements in the Chrome console:

```javascript
A = {};
B = {};
A.a = B;
B.b = A;
```

　　Then, output A, and we get an infinitely nested object:

　　(It's not actually infinitely nested, but rather two mutually pointing pointers.)

## Main Topic: Self-printing Programs

　　Let's first look at the popular C language version online:

```c
char*s="char*s=%c%s%c;main(){printf(s,34,s,34);}";main(){printf(s,34,s,34);}
```

　　The final printf function outputs the source code of the entire program. The '34' in printf is the ASCII code for the double quote " character, which is used to avoid issues with writing quotes within quotes. Cleverly using `%s` to represent the entire string in formatted output is the **core** of this self-replicating program: only by self-referencing in this way can the program output itself with limited code.
　　Referencing the online C language version, I got an idea for writing it in JavaScript: a method must be used to represent the entire string. However, JavaScript doesn't seem to have a command for formatted output, so we must find a way to use a very short string to represent the entire string. I thought of using a random character (like "@") as a placeholder in the string, and then using the string's `split` function (for string splitting and finding) to replace "@" with the entire string for output. So, let's first write a function to do this:

```javascript
function r(s){
	j = String.fromCharCode(34); //j represents a double quote
	k = '@';
	q = s.split(k);
	return q[0]+k+q[1]+j+s+j+q[2] //Returns the string where '@' in 's' is replaced by 's', e.g., "abc@d" becomes "abc@d" + "abc@d" + "d" (meaning "abc" + s + "d")
}
```

　　Next, we need to write that string. We don't know what that string looks like yet, so let's look at the next step, which is also the final step – outputting the string: We use the `console.log` function to output the final result, so the last line of code is `console.log(r(s))`, meaning to output the replaced string. Our code now looks like this:

```javascript
function r(s){
	j = String.fromCharCode(34); 
	k = '@';
	q = s.split(k);
	return q[0]+k+q[1]+j+s+j+q[2]
}
s = .....
console.log(r(s))
```

　　Now we know what string `s` should be equal to, right? It should be equal to all the code text above (because our goal is to output all the code). The ellipses above are placeholders for "@", which will allow the code to describe itself. So the final code is as follows:

```javascript
function r(s){j=String.fromCharCode(34);k='@';q=s.split(k);return q[0]+k+q[1]+j+s+j+q[2]}s="function r(s){j=String.fromCharCode(34);k='@';q=s.split(k);return q[0]+k+q[1]+j+s+j+q[2]}s=@;console.log(r(s))";console.log(r(s))
```

　　We are aiming for brevity, so there's no need for a separate function. Optimizing it, we get:

```javascript
s="s=@;j=String.fromCharCode(34);k='@';q=s.split(k);z=q[0]+j+s+j+q[1]+k+q[2];console.log(z)";j=String.fromCharCode(34);k='@';q=s.split(k);z=q[0]+j+s+j+q[1]+k+q[2];console.log(z)
```

　　To fully leverage JavaScript's features to make the code shorter, we can also use the **eval function** mentioned earlier. We see a large amount of code appearing twice – first as the code itself, and second inside the string `s`. We actually only need to write this string content once: one copy becomes the code itself using `eval`, and the other is assigned to string `s`. Simplified, it's as follows:

```javascript
x="j=String.fromCharCode(34)";y="k='@';q=s.split(k);z='x='+j+x+j+';y='+j+y+j+';eval(x);s='+j+q[0]+k+q[1]+j+q[1];console.log(z);";eval(x);s="s=@;eval(y)";eval(y)
```

　　Further optimization:

```javascript
j=String.fromCharCode(34);y="k='@';q=s.split(k);z='j=String.fromCharCode(34);y='+j+y+j+';s='+j+q[0]+k+q[1]+j+q[1];console.log(z);";s="s=@;eval(y)";eval(y)
```

　　It still doesn't feel short enough. JavaScript has another advantage we haven't utilized yet: the nested use of single and double quotes. If we use it well, we won't need such long code like `String.fromCharCode(34)` to represent quotes. The optimized result is as follows:

```javascript
x="'";v='"';y="k='@';q=s.split(k);z='x='+j+x+j+';v='+x+v+x+';y='+j+y+j+';s='+j+q[0]+k+q[1]+j+q[1];console.log(z);";s="s=@;eval(y);";eval(y);
```

　　We also don't need to specifically create a symbol "@"; we can just use an existing single quote as a marker, saving a few characters:

```javascript
x="'";v='"';y="q=s.split(x);z='x='+v+x+v+';v='+x+v+x+';y='+v+y+v+';s='+v+q[0]+x+q[1]+v+q[1];console.log(z);";s="s=';eval(y)";eval(y)
```

　　The single quote in the string `s=';eval(y)` is found by `s.split(x)` and then replaced. Of course, we can also use the more readily available `replace` function to replace strings. But then I was stunned when I saw the shortest JavaScript code on this [website](http://www.nyx.net/~gthompso/quine.htm):

```javascript
function a(){console.log(a+"a()")}a()
```

　　Its principle is very simple: directly `console.log` the function variable `a`, and then JavaScript automatically converts `a` into a string, which directly becomes the source code of function `a`. Of course, some say this is a cheating method... In fact, using the `eval` function itself might be considered cheating...

```