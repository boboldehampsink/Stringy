[![Build Status](https://api.travis-ci.org/voku/Stringy.svg?branch=master)](https://travis-ci.org/voku/Stringy)
[![codecov.io](https://codecov.io/github/voku/Stringy/coverage.svg?branch=master)](https://codecov.io/github/voku/Stringy?branch=master)
[![Codacy Badge](https://api.codacy.com/project/badge/grade/97c46467e585467d884bac1130cb45e5)](https://www.codacy.com/app/voku/Stringy)
[![Latest Stable Version](https://poser.pugx.org/voku/stringy/v/stable)](https://packagist.org/packages/voku/stringy)
[![Total Downloads](https://poser.pugx.org/voku/stringy/downloads)](https://packagist.org/packages/voku/stringy) 
[![License](https://poser.pugx.org/voku/stringy/license)](https://packagist.org/packages/voku/stringy)
[![Donate to this project using Paypal](https://img.shields.io/badge/paypal-donate-yellow.svg)](https://www.paypal.me/moelleken)
[![Donate to this project using Patreon](https://img.shields.io/badge/patreon-donate-yellow.svg)](https://www.patreon.com/voku)

## :accept: Stringy 

A PHP string manipulation library with multibyte support. Compatible with PHP 7+

100% compatible with the original "[Stringy](https://github.com/danielstjules/Stringy)" library, but this fork is optimized 
for performance and is using PHP 7+ features. 

``` php
s('string')->toTitleCase()->ensureRight('y') == 'Stringy'
```

* [Why?](#why)
* [Alternative](#alternative)
* [Installation](#installation-via-composer-require)
* [OO and Chaining](#oo-and-chaining)
* [Implemented Interfaces](#implemented-interfaces)
* [PHP Class Call Creation](#php-class-call-creation)
* [Class methods](#class-methods)
    * [create](#createmixed-str--encoding-)
* [Instance methods](#instance-methods)
* [Extensions](#extensions)
* [Tests](#tests)
* [License](#license)

## Why?

In part due to a lack of multibyte support (including UTF-8) across many of
PHP's standard string functions. But also to offer an OO wrapper around the
`mbstring` module's multibyte-compatible functions. Stringy handles some quirks,
provides additional functionality, and hopefully makes strings a little easier
to work with!

```php
// Standard library
strtoupper('fòôbàř');       // 'FòôBàř'
strlen('fòôbàř');           // 10

// mbstring
mb_strtoupper('fòôbàř');    // 'FÒÔBÀŘ'
mb_strlen('fòôbàř');        // '6'

// Stringy
$stringy = Stringy\Stringy::create('fòôbàř');
$stringy->toUpperCase();    // 'FÒÔBÀŘ'
$stringy->length();         // '6'
```

## Alternative

If you like a more Functional Way to edit strings, then you can take a look at [voku/portable-utf8](https://github.com/voku/portable-utf8), also "voku/Stringy" used the functions from the "Portable UTF-8"-Class but in a more Object Oriented Way.

```php
// Portable UTF-8
use voku\helper\UTF8;
UTF8::strtoupper('fòôbàř');    // 'FÒÔBÀŘ'
UTF8::strlen('fòôbàř');        // '6'
```

## Installation via "composer require"
```shell
composer require voku/stringy
```

## Installation via composer (manually)

If you're using Composer to manage dependencies, you can include the following
in your composer.json file:

```json
"require": {
    "voku/stringy": "~6.0"
}
```

Then, after running `composer update` or `php composer.phar update`, you can
load the class using Composer's autoloading:

```php
require 'vendor/autoload.php';
```

Otherwise, you can simply require the file directly:

```php
require_once 'path/to/Stringy/src/Stringy.php';
```

And in either case, I'd suggest using an alias.

```php
use Stringy\Stringy as S;
```

## OO and Chaining

The library offers OO method chaining, as seen below:

```php
use Stringy\Stringy as S;
echo S::create('fòô     bàř')->collapseWhitespace()->swapCase(); // 'FÒÔ BÀŘ'
```

`Stringy\Stringy` has a __toString() method, which returns the current string
when the object is used in a string context, ie:
`(string) S::create('foo')  // 'foo'`

## Implemented Interfaces

`Stringy\Stringy` implements the `IteratorAggregate` interface, meaning that
`foreach` can be used with an instance of the class:

``` php
$stringy = S::create('fòôbàř');
foreach ($stringy as $char) {
    echo $char;
}
// 'fòôbàř'
```

It implements the `Countable` interface, enabling the use of `count()` to
retrieve the number of characters in the string:

``` php
$stringy = S::create('fòô');
count($stringy);  // 3
```

Furthermore, the `ArrayAccess` interface has been implemented. As a result,
`isset()` can be used to check if a character at a specific index exists. And
since `Stringy\Stringy` is immutable, any call to `offsetSet` or `offsetUnset`
will throw an exception. `offsetGet` has been implemented, however, and accepts
both positive and negative indexes. Invalid indexes result in an
`OutOfBoundsException`.

``` php
$stringy = S::create('bàř');
echo $stringy[2];     // 'ř'
echo $stringy[-2];    // 'à'
isset($stringy[-4]);  // false

$stringy[3];          // OutOfBoundsException
$stringy[2] = 'a';    // Exception
```

## PHP Class Call Creation

As of PHP 5.6+, [`use function`](https://wiki.php.net/rfc/use_function) is
available for importing functions. Stringy exposes a namespaced function,
`Stringy\create`, which emits the same behaviour as `Stringy\Stringy::create()`.

``` php
use function Stringy\create as s;

// Instead of: S::create('fòô     bàř')
s('fòô     bàř')->collapseWhitespace()->swapCase();
```

## Class methods

##### create(mixed $str [, $encoding ])

Creates a Stringy object and assigns both str and encoding properties
the supplied values. $str is cast to a string prior to assignment, and if
$encoding is not specified, it defaults to mb_internal_encoding(). It
then returns the initialized object. Throws an InvalidArgumentException
if the first argument is an array or object without a __toString method.

```php
$stringy = S::create('fòôbàř', 'UTF-8'); // 'fòôbàř'
```

If you need a collection of Stringy objects you can use the S::collection()
method. 

```php
$stringyCollection = \Stringy\collection(['fòôbàř', 'lall', 'öäü']);
```

## Instance Methods

Stringy objects are immutable. All examples below make use of PHP 5.6
function importing, and PHP 5.4 short array syntax. They also assume the
encoding returned by mb_internal_encoding() is UTF-8. For further details,
see the documentation for the create method above.


<table>
    <tr><td><a href="#afterstring-string-static">after</a>
</td><td><a href="#afterfirststring-separator-static">afterFirst</a>
</td><td><a href="#afterfirstignorecasestring-separator-static">afterFirstIgnoreCase</a>
</td><td><a href="#afterlaststring-separator-static">afterLast</a>
</td></tr><tr><td><a href="#afterlastignorecasestring-separator-static">afterLastIgnoreCase</a>
</td><td><a href="#appendstring-suffix-static">append</a>
</td><td><a href="#appendpasswordint-length-static">appendPassword</a>
</td><td><a href="#appendrandomstringint-length-string-possiblechars-static">appendRandomString</a>
</td></tr><tr><td><a href="#appendstringycollectionstringystatic-suffix-static">appendStringy</a>
</td><td><a href="#appenduniqueidentifierintstring-entropyextra-bool-md5-static">appendUniqueIdentifier</a>
</td><td><a href="#atint-index-static">at</a>
</td><td><a href="#base64decode-self">base64Decode</a>
</td></tr><tr><td><a href="#base64encode-self">base64Encode</a>
</td><td><a href="#bcrypt-options-static">bcrypt</a>
</td><td><a href="#beforestring-string-static">before</a>
</td><td><a href="#beforefirststring-separator-static">beforeFirst</a>
</td></tr><tr><td><a href="#beforefirstignorecasestring-separator-static">beforeFirstIgnoreCase</a>
</td><td><a href="#beforelaststring-separator-static">beforeLast</a>
</td><td><a href="#beforelastignorecasestring-separator-static">beforeLastIgnoreCase</a>
</td><td><a href="#betweenstring-start-string-end-int-offset-static">between</a>
</td></tr><tr><td><a href="#calluserfunctioncallable-function-mixed-parameter-static">callUserFunction</a>
</td><td><a href="#camelize-static">camelize</a>
</td><td><a href="#capitalizepersonalname-static">capitalizePersonalName</a>
</td><td><a href="#chars-string">chars</a>
</td></tr><tr><td><a href="#chunkint-length-static">chunk</a>
</td><td><a href="#chunkcollectionint-length-collectionstringystatic">chunkCollection</a>
</td><td><a href="#collapsewhitespace-static">collapseWhitespace</a>
</td><td><a href="#containsstring-needle-bool-casesensitive-bool">contains</a>
</td></tr><tr><td><a href="#containsallstring-needles-bool-casesensitive-bool">containsAll</a>
</td><td><a href="#containsanystring-needles-bool-casesensitive-bool">containsAny</a>
</td><td><a href="#count-int">count</a>
</td><td><a href="#countsubstrstring-substring-bool-casesensitive-int">countSubstr</a>
</td></tr><tr><td><a href="#crc32-int">crc32</a>
</td><td><a href="#createmixed-str-string-encoding-static">create</a>
</td><td><a href="#cryptstring-salt-static">crypt</a>
</td><td><a href="#dasherize-static">dasherize</a>
</td></tr><tr><td><a href="#decryptstring-password-static">decrypt</a>
</td><td><a href="#delimitstring-delimiter-static">delimit</a>
</td><td><a href="#encodestring-new_encoding-bool-auto_detect_encoding-static">encode</a>
</td><td><a href="#encryptstring-password-static">encrypt</a>
</td></tr><tr><td><a href="#endswithstring-substring-bool-casesensitive-bool">endsWith</a>
</td><td><a href="#endswithanystring-substrings-bool-casesensitive-bool">endsWithAny</a>
</td><td><a href="#ensureleftstring-substring-static">ensureLeft</a>
</td><td><a href="#ensurerightstring-substring-static">ensureRight</a>
</td></tr><tr><td><a href="#escape-static">escape</a>
</td><td><a href="#explodestring-delimiter-int-limit-arrayintstatic">explode</a>
</td><td><a href="#explodecollectionstring-delimiter-int-limit-collectionstringystatic">explodeCollection</a>
</td><td><a href="#extracttextstring-search-intnull-length-string-replacerforskippedtext-static">extractText</a>
</td></tr><tr><td><a href="#firstint-n-static">first</a>
</td><td><a href="#formatmixed-args-static">format</a>
</td><td><a href="#getencoding-string">getEncoding</a>
</td><td><a href="#getiterator-arrayiterator">getIterator</a>
</td></tr><tr><td><a href="#hardwrapint-width-string-break-static">hardWrap</a>
</td><td><a href="#haslowercase-bool">hasLowerCase</a>
</td><td><a href="#hasuppercase-bool">hasUpperCase</a>
</td><td><a href="#hashstring-algorithm-static">hash</a>
</td></tr><tr><td><a href="#hexdecode-static">hexDecode</a>
</td><td><a href="#hexencode-static">hexEncode</a>
</td><td><a href="#htmldecodeint-flags-static">htmlDecode</a>
</td><td><a href="#htmlencodeint-flags-static">htmlEncode</a>
</td></tr><tr><td><a href="#humanize-static">humanize</a>
</td><td><a href="#instring-str-bool-casesensitive-bool">in</a>
</td><td><a href="#indexofstring-needle-int-offset-falseint">indexOf</a>
</td><td><a href="#indexofignorecasestring-needle-int-offset-falseint">indexOfIgnoreCase</a>
</td></tr><tr><td><a href="#indexoflaststring-needle-int-offset-falseint">indexOfLast</a>
</td><td><a href="#indexoflastignorecasestring-needle-int-offset-falseint">indexOfLastIgnoreCase</a>
</td><td><a href="#insertstring-substring-int-index-static">insert</a>
</td><td><a href="#isstring-pattern-bool">is</a>
</td></tr><tr><td><a href="#isalpha-bool">isAlpha</a>
</td><td><a href="#isalphanumeric-bool">isAlphanumeric</a>
</td><td><a href="#isbase64bool-emptystringisvalid-bool">isBase64</a>
</td><td><a href="#isblank-bool">isBlank</a>
</td></tr><tr><td><a href="#isemailbool-useexampledomaincheck-bool-usetypoindomaincheck-bool-usetemporarydomaincheck-bool-usednscheck-bool">isEmail</a>
</td><td><a href="#isempty-bool">isEmpty</a>
</td><td><a href="#isequalsstringstringy-str-bool">isEquals</a>
</td><td><a href="#isequalscaseinsensitivefloatintstringstringy-str-bool">isEqualsCaseInsensitive</a>
</td></tr><tr><td><a href="#isequalscasesensitivefloatintstringstringy-str-bool">isEqualsCaseSensitive</a>
</td><td><a href="#ishexadecimal-bool">isHexadecimal</a>
</td><td><a href="#ishtml-bool">isHtml</a>
</td><td><a href="#isjsonbool-onlyarrayorobjectresultsarevalid-bool">isJson</a>
</td></tr><tr><td><a href="#islowercase-bool">isLowerCase</a>
</td><td><a href="#isnotempty-bool">isNotEmpty</a>
</td><td><a href="#isnumeric-bool">isNumeric</a>
</td><td><a href="#isprintable-bool">isPrintable</a>
</td></tr><tr><td><a href="#ispunctuation-bool">isPunctuation</a>
</td><td><a href="#isserialized-bool">isSerialized</a>
</td><td><a href="#issimilarstring-str-float-minpercentforsimilarity-bool">isSimilar</a>
</td><td><a href="#isuppercase-bool">isUpperCase</a>
</td></tr><tr><td><a href="#iswhitespace-bool">isWhitespace</a>
</td><td><a href="#jsonserialize-string">jsonSerialize</a>
</td><td><a href="#kebabcase-static">kebabCase</a>
</td><td><a href="#lastint-n-static">last</a>
</td></tr><tr><td><a href="#lastsubstringofstring-needle-bool-beforeneedle-static">lastSubstringOf</a>
</td><td><a href="#lastsubstringofignorecasestring-needle-bool-beforeneedle-static">lastSubstringOfIgnoreCase</a>
</td><td><a href="#length-int">length</a>
</td><td><a href="#linewrapint-limit-string-break-bool-add_final_break-stringnull-delimiter-static">lineWrap</a>
</td></tr><tr><td><a href="#linewrapafterwordint-limit-string-break-bool-add_final_break-stringnull-delimiter-static">lineWrapAfterWord</a>
</td><td><a href="#lines-static">lines</a>
</td><td><a href="#linescollection-collectionstringystatic">linesCollection</a>
</td><td><a href="#longestcommonprefixstring-otherstr-static">longestCommonPrefix</a>
</td></tr><tr><td><a href="#longestcommonsubstringstring-otherstr-static">longestCommonSubstring</a>
</td><td><a href="#longestcommonsuffixstring-otherstr-static">longestCommonSuffix</a>
</td><td><a href="#lowercasefirst-static">lowerCaseFirst</a>
</td><td><a href="#matchcaseinsensitivestringstringy-str-bool">matchCaseInsensitive</a>
</td></tr><tr><td><a href="#matchcasesensitivestringstringy-str-bool">matchCaseSensitive</a>
</td><td><a href="#md5-static">md5</a>
</td><td><a href="#newlinetohtmlbreak-static">newLineToHtmlBreak</a>
</td><td><a href="#nthint-step-int-offset-static">nth</a>
</td></tr><tr><td><a href="#offsetexistsint-offset-bool">offsetExists</a>
</td><td><a href="#offsetgetint-offset-string">offsetGet</a>
</td><td><a href="#offsetsetint-offset-mixed-value-void">offsetSet</a>
</td><td><a href="#offsetunsetint-offset-void">offsetUnset</a>
</td></tr><tr><td><a href="#padint-length-string-padstr-string-padtype-static">pad</a>
</td><td><a href="#padbothint-length-string-padstr-static">padBoth</a>
</td><td><a href="#padleftint-length-string-padstr-static">padLeft</a>
</td><td><a href="#padrightint-length-string-padstr-static">padRight</a>
</td></tr><tr><td><a href="#pascalcase-static">pascalCase</a>
</td><td><a href="#prependstring-prefix-static">prepend</a>
</td><td><a href="#prependstringycollectionstringystatic-prefix-static">prependStringy</a>
</td><td><a href="#regexreplacestring-pattern-string-replacement-string-options-string-delimiter-static">regexReplace</a>
</td></tr><tr><td><a href="#removehtmlstring-allowabletags-static">removeHtml</a>
</td><td><a href="#removehtmlbreakstring-replacement-static">removeHtmlBreak</a>
</td><td><a href="#removeleftstring-substring-static">removeLeft</a>
</td><td><a href="#removerightstring-substring-static">removeRight</a>
</td></tr><tr><td><a href="#removexss-static">removeXss</a>
</td><td><a href="#repeatint-multiplier-static">repeat</a>
</td><td><a href="#replacestring-search-string-replacement-bool-casesensitive-static">replace</a>
</td><td><a href="#replaceallstring-search-stringstring-replacement-bool-casesensitive-static">replaceAll</a>
</td></tr><tr><td><a href="#replacebeginningstring-search-string-replacement-static">replaceBeginning</a>
</td><td><a href="#replaceendingstring-search-string-replacement-static">replaceEnding</a>
</td><td><a href="#replacefirststring-search-string-replacement-static">replaceFirst</a>
</td><td><a href="#replacelaststring-search-string-replacement-static">replaceLast</a>
</td></tr><tr><td><a href="#reverse-static">reverse</a>
</td><td><a href="#safetruncateint-length-string-substring-bool-ignoredonotsplitwordsforoneword-static">safeTruncate</a>
</td><td><a href="#setinternalencodingstring-new_encoding-static">setInternalEncoding</a>
</td><td><a href="#sha1-static">sha1</a>
</td></tr><tr><td><a href="#sha256-static">sha256</a>
</td><td><a href="#sha512-static">sha512</a>
</td><td><a href="#shortenafterwordint-length-string-straddon-static">shortenAfterWord</a>
</td><td><a href="#shuffle-static">shuffle</a>
</td></tr><tr><td><a href="#similaritystring-str-float">similarity</a>
</td><td><a href="#sliceint-start-int-end-static">slice</a>
</td><td><a href="#slugifystring-separator-string-language-arraystringstring-replacements-bool-replace_extra_symbols-bool-use_str_to_lower-bool-use_transliterate-static">slugify</a>
</td><td><a href="#snakecase-static">snakeCase</a>
</td></tr><tr><td><a href="#snakeize-static">snakeize</a>
</td><td><a href="#softwrapint-width-string-break-static">softWrap</a>
</td><td><a href="#splitstring-pattern-int-limit-static">split</a>
</td><td><a href="#splitcollectionstring-pattern-int-limit-collectionstringystatic">splitCollection</a>
</td></tr><tr><td><a href="#startswithstring-substring-bool-casesensitive-bool">startsWith</a>
</td><td><a href="#startswithanystring-substrings-bool-casesensitive-bool">startsWithAny</a>
</td><td><a href="#stripstringstring-search-static">strip</a>
</td><td><a href="#stripwhitespace-static">stripWhitespace</a>
</td></tr><tr><td><a href="#stripecssmediaqueries-static">stripeCssMediaQueries</a>
</td><td><a href="#stripeemptyhtmltags-static">stripeEmptyHtmlTags</a>
</td><td><a href="#studlycase-static">studlyCase</a>
</td><td><a href="#substrint-start-int-length-static">substr</a>
</td></tr><tr><td><a href="#substringint-start-int-length-static">substring</a>
</td><td><a href="#substringofstring-needle-bool-beforeneedle-static">substringOf</a>
</td><td><a href="#substringofignorecasestring-needle-bool-beforeneedle-static">substringOfIgnoreCase</a>
</td><td><a href="#surroundstring-substring-static">surround</a>
</td></tr><tr><td><a href="#swapcase-static">swapCase</a>
</td><td><a href="#tidy-static">tidy</a>
</td><td><a href="#titleizearraystringnull-ignore-stringnull-word_define_chars-stringnull-language-static">titleize</a>
</td><td><a href="#titleizeforhumansstring-ignore-static">titleizeForHumans</a>
</td></tr><tr><td><a href="#toasciistring-language-bool-removeunsupported-static">toAscii</a>
</td><td><a href="#toboolean-bool">toBoolean</a>
</td><td><a href="#tolowercasebool-trytokeepstringlength-stringnull-lang-static">toLowerCase</a>
</td><td><a href="#tospacesint-tablength-static">toSpaces</a>
</td></tr><tr><td><a href="#tostring-string">toString</a>
</td><td><a href="#totabsint-tablength-static">toTabs</a>
</td><td><a href="#totitlecase-static">toTitleCase</a>
</td><td><a href="#totransliteratebool-strict-string-unknown-static">toTransliterate</a>
</td></tr><tr><td><a href="#touppercasebool-trytokeepstringlength-stringnull-lang-static">toUpperCase</a>
</td><td><a href="#trimstring-chars-static">trim</a>
</td><td><a href="#trimleftstring-chars-static">trimLeft</a>
</td><td><a href="#trimrightstring-chars-static">trimRight</a>
</td></tr><tr><td><a href="#truncateint-length-string-substring-static">truncate</a>
</td><td><a href="#underscored-static">underscored</a>
</td><td><a href="#uppercamelize-static">upperCamelize</a>
</td><td><a href="#uppercasefirst-static">upperCaseFirst</a>
</td></tr><tr><td><a href="#urldecode-static">urlDecode</a>
</td><td><a href="#urldecodemulti-static">urlDecodeMulti</a>
</td><td><a href="#urldecoderaw-static">urlDecodeRaw</a>
</td><td><a href="#urldecoderawmulti-static">urlDecodeRawMulti</a>
</td></tr><tr><td><a href="#urlencode-static">urlEncode</a>
</td><td><a href="#urlencoderaw-static">urlEncodeRaw</a>
</td><td><a href="#urlifystring-separator-string-language-arraystringstring-replacements-bool-strtolower-static">urlify</a>
</td><td><a href="#utf8ify-static">utf8ify</a>
</td></tr><tr><td><a href="#wordsstring-char_list-bool-remove_empty_values-intnull-remove_short_values-static">words</a>
</td><td><a href="#wordscollectionstring-char_list-bool-remove_empty_values-intnull-remove_short_values-collectionstringystatic">wordsCollection</a>
</td><td><a href="#wrapstring-substring-static">wrap</a>
</td></tr>
</table>


## after(string $string): static
<a href="#class-methods">↑</a>
Return part of the string occurring after a specific string.

EXAMPLE: <code>
s('宮本 茂')->after('本'); // ' 茂'
</code>

**Parameters:**
- `string $string <p>The delimiting string.</p>`

**Return:**
- `static`

--------

## afterFirst(string $separator): static
<a href="#class-methods">↑</a>
Gets the substring after the first occurrence of a separator.

If no match is found returns new empty Stringy object.

EXAMPLE: <code>
s('</b></b>')->afterFirst('b'); // '></b>'
</code>

**Parameters:**
- `string $separator`

**Return:**
- `static`

--------

## afterFirstIgnoreCase(string $separator): static
<a href="#class-methods">↑</a>
Gets the substring after the first occurrence of a separator.

If no match is found returns new empty Stringy object.

EXAMPLE: <code>
s('</B></B>')->afterFirstIgnoreCase('b'); // '></B>'
</code>

**Parameters:**
- `string $separator`

**Return:**
- `static`

--------

## afterLast(string $separator): static
<a href="#class-methods">↑</a>
Gets the substring after the last occurrence of a separator.

If no match is found returns new empty Stringy object.

EXAMPLE: <code>
s('</b></b>')->afterLast('b'); // '>'
</code>

**Parameters:**
- `string $separator`

**Return:**
- `static`

--------

## afterLastIgnoreCase(string $separator): static
<a href="#class-methods">↑</a>
Gets the substring after the last occurrence of a separator.

If no match is found returns new empty Stringy object.

EXAMPLE: <code>
s('</B></B>')->afterLastIgnoreCase('b'); // '>'
</code>

**Parameters:**
- `string $separator`

**Return:**
- `static`

--------

## append(string $suffix): static
<a href="#class-methods">↑</a>
Returns a new string with $suffix appended.

EXAMPLE: <code>
s('fòô')->append('bàř'); // 'fòôbàř'
</code>

**Parameters:**
- `string ...$suffix <p>The string to append.</p>`

**Return:**
- `static <p>Object with appended $suffix.</p>`

--------

## appendPassword(int $length): static
<a href="#class-methods">↑</a>
Append an password (limited to chars that are good readable).

EXAMPLE: <code>
s('')->appendPassword(8); // e.g.: '89bcdfgh'
</code>

**Parameters:**
- `int $length <p>Length of the random string.</p>`

**Return:**
- `static <p>Object with appended password.</p>`

--------

## appendRandomString(int $length, string $possibleChars): static
<a href="#class-methods">↑</a>
Append an random string.

EXAMPLE: <code>
s('')->appendUniqueIdentifier(5, 'ABCDEFGHI'); // e.g.: 'CDEHI'
</code>

**Parameters:**
- `int $length <p>Length of the random string.</p>`
- `string $possibleChars [optional] <p>Characters string for the random selection.</p>`

**Return:**
- `static <p>Object with appended random string.</p>`

--------

## appendStringy(\CollectionStringy|static $suffix): static
<a href="#class-methods">↑</a>
Returns a new string with $suffix appended.

EXAMPLE: <code>
</code>

**Parameters:**
- `CollectionStringy|static ...$suffix <p>The Stringy objects to append.</p>`

**Return:**
- `static <p>Object with appended $suffix.</p>`

--------

## appendUniqueIdentifier(int|string $entropyExtra, bool $md5): static
<a href="#class-methods">↑</a>
Append an unique identifier.

EXAMPLE: <code>
s('')->appendUniqueIdentifier(); // e.g.: '1f3870be274f6c49b3e31a0c6728957f'
</code>

**Parameters:**
- `int|string $entropyExtra [optional] <p>Extra entropy via a string or int value.</p>`
- `bool $md5 [optional] <p>Return the unique identifier as md5-hash? Default: true</p>`

**Return:**
- `static <p>Object with appended unique identifier as md5-hash.</p>`

--------

## at(int $index): static
<a href="#class-methods">↑</a>
Returns the character at $index, with indexes starting at 0.

EXAMPLE: <code>
s('fòôbàř')->at(3); // 'b'
</code>

**Parameters:**
- `int $index <p>Position of the character.</p>`

**Return:**
- `static <p>The character at $index.</p>`

--------

## base64Decode(): self
<a href="#class-methods">↑</a>
Decode the base64 encoded string.

EXAMPLE: <code>
</code>

**Parameters:**
__nothing__

**Return:**
- `self`

--------

## base64Encode(): self
<a href="#class-methods">↑</a>
Encode the string to base64.

EXAMPLE: <code>
</code>

**Parameters:**
__nothing__

**Return:**
- `self`

--------

## bcrypt( $options): static
<a href="#class-methods">↑</a>
Creates a hash from the string using the CRYPT_BLOWFISH algorithm.

WARNING: Using this algorithm, will result in the ```$this->str```
         being truncated to a maximum length of 72 characters.

EXAMPLE: <code>
</code>

**Parameters:**
- ``

**Return:**
- `static`

--------

## before(string $string): static
<a href="#class-methods">↑</a>
Return part of the string occurring before a specific string.

EXAMPLE: <code>
</code>

**Parameters:**
- `string $string <p>The delimiting string.</p>`

**Return:**
- `static`

--------

## beforeFirst(string $separator): static
<a href="#class-methods">↑</a>
Gets the substring before the first occurrence of a separator.

If no match is found returns new empty Stringy object.

EXAMPLE: <code>
s('</b></b>')->beforeFirst('b'); // '</'
</code>

**Parameters:**
- `string $separator`

**Return:**
- `static`

--------

## beforeFirstIgnoreCase(string $separator): static
<a href="#class-methods">↑</a>
Gets the substring before the first occurrence of a separator.

If no match is found returns new empty Stringy object.

EXAMPLE: <code>
s('</B></B>')->beforeFirstIgnoreCase('b'); // '</'
</code>

**Parameters:**
- `string $separator`

**Return:**
- `static`

--------

## beforeLast(string $separator): static
<a href="#class-methods">↑</a>
Gets the substring before the last occurrence of a separator.

If no match is found returns new empty Stringy object.

EXAMPLE: <code>
s('</b></b>')->beforeLast('b'); // '</b></'
</code>

**Parameters:**
- `string $separator`

**Return:**
- `static`

--------

## beforeLastIgnoreCase(string $separator): static
<a href="#class-methods">↑</a>
Gets the substring before the last occurrence of a separator.

If no match is found returns new empty Stringy object.

EXAMPLE: <code>
s('</B></B>')->beforeLastIgnoreCase('b'); // '</B></'
</code>

**Parameters:**
- `string $separator`

**Return:**
- `static`

--------

## between(string $start, string $end, int $offset): static
<a href="#class-methods">↑</a>
Returns the substring between $start and $end, if found, or an empty
string. An optional offset may be supplied from which to begin the
search for the start string.

EXAMPLE: <code>
s('{foo} and {bar}')->between('{', '}'); // 'foo'
</code>

**Parameters:**
- `string $start <p>Delimiter marking the start of the substring.</p>`
- `string $end <p>Delimiter marking the end of the substring.</p>`
- `int $offset [optional] <p>Index from which to begin the search. Default: 0</p>`

**Return:**
- `static <p>Object whose $str is a substring between $start and $end.</p>`

--------

## callUserFunction(callable $function, mixed $parameter): static
<a href="#class-methods">↑</a>
Call a user function.

EXAMPLE: <code>
S::create('foo bar lall')->callUserFunction(static function ($str) {
    return UTF8::str_limit($str, 8);
})->toString(); // "foo bar…"
</code>

**Parameters:**
- `callable $function`
- `mixed ...$parameter`

**Return:**
- `static <p>Object having a $str changed via $function.</p>`

--------

## camelize(): static
<a href="#class-methods">↑</a>
Returns a camelCase version of the string. Trims surrounding spaces,
capitalizes letters following digits, spaces, dashes and underscores,
and removes spaces, dashes, as well as underscores.

EXAMPLE: <code>
s('Camel-Case')->camelize(); // 'camelCase'
</code>

**Parameters:**
__nothing__

**Return:**
- `static <p>Object with $str in camelCase.</p>`

--------

## capitalizePersonalName(): static
<a href="#class-methods">↑</a>
Returns the string with the first letter of each word capitalized,
except for when the word is a name which shouldn't be capitalized.

EXAMPLE: <code>
s('jaap de hoop scheffer')->capitalizePersonName(); // 'Jaap de Hoop Scheffer'
</code>

**Parameters:**
__nothing__

**Return:**
- `static <p>Object with $str capitalized.</p>`

--------

## chars(): string[]
<a href="#class-methods">↑</a>
Returns an array consisting of the characters in the string.

EXAMPLE: <code>
s('fòôbàř')->chars(); // ['f', 'ò', 'ô', 'b', 'à', 'ř']
</code>

**Parameters:**
__nothing__

**Return:**
- `string[] <p>An array of string chars.</p>`

--------

## chunk(int $length): static[]
<a href="#class-methods">↑</a>
Splits the string into chunks of Stringy objects.

EXAMPLE: <code>
s('foobar')->chunk(3); // ['foo', 'bar']
</code>

**Parameters:**
- `int $length [optional] <p>Max character length of each array element.</p>`

**Return:**
- `static[] <p>An array of Stringy objects.</p>`

--------

## chunkCollection(int $length): \CollectionStringy|static[]
<a href="#class-methods">↑</a>
Splits the string into chunks of Stringy objects collection.

EXAMPLE: <code>
</code>

**Parameters:**
- `int $length [optional] <p>Max character length of each array element.</p>`

**Return:**
- `\CollectionStringy|static[] <p>An collection of Stringy objects.</p>`

--------

## collapseWhitespace(): static
<a href="#class-methods">↑</a>
Trims the string and replaces consecutive whitespace characters with a
single space. This includes tabs and newline characters, as well as
multibyte whitespace such as the thin space and ideographic space.

EXAMPLE: <code>
s('   Ο     συγγραφέας  ')->collapseWhitespace(); // 'Ο συγγραφέας'
</code>

**Parameters:**
__nothing__

**Return:**
- `static <p>Object with a trimmed $str and condensed whitespace.</p>`

--------

## contains(string $needle, bool $caseSensitive): bool
<a href="#class-methods">↑</a>
Returns true if the string contains $needle, false otherwise. By default
the comparison is case-sensitive, but can be made insensitive by setting
$caseSensitive to false.

EXAMPLE: <code>
s('Ο συγγραφέας είπε')->contains('συγγραφέας'); // true
</code>

**Parameters:**
- `string $needle <p>Substring to look for.</p>`
- `bool $caseSensitive [optional] <p>Whether or not to enforce case-sensitivity. Default: true</p>`

**Return:**
- `bool <p>Whether or not $str contains $needle.</p>`

--------

## containsAll(string[] $needles, bool $caseSensitive): bool
<a href="#class-methods">↑</a>
Returns true if the string contains all $needles, false otherwise. By
default the comparison is case-sensitive, but can be made insensitive by
setting $caseSensitive to false.

EXAMPLE: <code>
s('foo & bar')->containsAll(['foo', 'bar']); // true
</code>

**Parameters:**
- `array<array-key, string> $needles <p>SubStrings to look for.</p>`
- `bool $caseSensitive [optional] <p>Whether or not to enforce case-sensitivity. Default: true</p>`

**Return:**
- `bool <p>Whether or not $str contains $needle.</p>`

--------

## containsAny(string[] $needles, bool $caseSensitive): bool
<a href="#class-methods">↑</a>
Returns true if the string contains any $needles, false otherwise. By
default the comparison is case-sensitive, but can be made insensitive by
setting $caseSensitive to false.

EXAMPLE: <code>
s('str contains foo')->containsAny(['foo', 'bar']); // true
</code>

**Parameters:**
- `array<array-key, string> $needles <p>SubStrings to look for.</p>`
- `bool $caseSensitive [optional] <p>Whether or not to enforce case-sensitivity. Default: true</p>`

**Return:**
- `bool <p>Whether or not $str contains $needle.</p>`

--------

## count(): int
<a href="#class-methods">↑</a>
Returns the length of the string, implementing the countable interface.

EXAMPLE: <code>
</code>

**Parameters:**
__nothing__

**Return:**
- `int <p>The number of characters in the string, given the encoding.</p>`

--------

## countSubstr(string $substring, bool $caseSensitive): int
<a href="#class-methods">↑</a>
Returns the number of occurrences of $substring in the given string.

By default, the comparison is case-sensitive, but can be made insensitive
by setting $caseSensitive to false.

EXAMPLE: <code>
s('Ο συγγραφέας είπε')->countSubstr('α'); // 2
</code>

**Parameters:**
- `string $substring <p>The substring to search for.</p>`
- `bool $caseSensitive [optional] <p>Whether or not to enforce case-sensitivity. Default: true</p>`

**Return:**
- `int`

--------

## crc32(): int
<a href="#class-methods">↑</a>
Calculates the crc32 polynomial of a string.

EXAMPLE: <code>
</code>

**Parameters:**
__nothing__

**Return:**
- `int`

--------

## create(mixed $str, string $encoding): static
<a href="#class-methods">↑</a>
Creates a Stringy object and assigns both str and encoding properties
the supplied values. $str is cast to a string prior to assignment, and if
$encoding is not specified, it defaults to mb_internal_encoding(). It
then returns the initialized object. Throws an InvalidArgumentException
if the first argument is an array or object without a __toString method.

**Parameters:**
- `mixed $str [optional] <p>Value to modify, after being cast to string. Default: ''</p>`
- `string $encoding [optional] <p>The character encoding. Fallback: 'UTF-8'</p>`

**Return:**
- `static <p>A Stringy object.</p>`

--------

## crypt(string $salt): static
<a href="#class-methods">↑</a>
One-way string encryption (hashing).

Hash the string using the standard Unix DES-based algorithm or an
alternative algorithm that may be available on the system.

PS: if you need encrypt / decrypt, please use ```static::encrypt($password)```
    and ```static::decrypt($password)```

EXAMPLE: <code>
</code>

**Parameters:**
- `string $salt <p>A salt string to base the hashing on.</p>`

**Return:**
- `static`

--------

## dasherize(): static
<a href="#class-methods">↑</a>
Returns a lowercase and trimmed string separated by dashes. Dashes are
inserted before uppercase characters (with the exception of the first
character of the string), and in place of spaces as well as underscores.

EXAMPLE: <code>
s('fooBar')->dasherize(); // 'foo-bar'
</code>

**Parameters:**
__nothing__

**Return:**
- `static <p>Object with a dasherized $str</p>`

--------

## decrypt(string $password): static
<a href="#class-methods">↑</a>
Decrypt the string.

EXAMPLE: <code>
</code>

**Parameters:**
- `string $password The key for decrypting`

**Return:**
- `static`

--------

## delimit(string $delimiter): static
<a href="#class-methods">↑</a>
Returns a lowercase and trimmed string separated by the given delimiter.

Delimiters are inserted before uppercase characters (with the exception
of the first character of the string), and in place of spaces, dashes,
and underscores. Alpha delimiters are not converted to lowercase.

EXAMPLE: <code>
s('fooBar')->delimit('::'); // 'foo::bar'
</code>

**Parameters:**
- `string $delimiter <p>Sequence used to separate parts of the string.</p>`

**Return:**
- `static <p>Object with a delimited $str.</p>`

--------

## encode(string $new_encoding, bool $auto_detect_encoding): static
<a href="#class-methods">↑</a>
Encode the given string into the given $encoding + set the internal character encoding.

EXAMPLE: <code>
</code>

**Parameters:**
- `string $new_encoding <p>The desired character encoding.</p>`
- `bool $auto_detect_encoding [optional] <p>Auto-detect the current string-encoding</p>`

**Return:**
- `static`

--------

## encrypt(string $password): static
<a href="#class-methods">↑</a>
Encrypt the string.

EXAMPLE: <code>
</code>

**Parameters:**
- `string $password <p>The key for encrypting</p>`

**Return:**
- `static`

--------

## endsWith(string $substring, bool $caseSensitive): bool
<a href="#class-methods">↑</a>
Returns true if the string ends with $substring, false otherwise. By
default, the comparison is case-sensitive, but can be made insensitive
by setting $caseSensitive to false.

EXAMPLE: <code>
s('fòôbàř')->endsWith('bàř', true); // true
</code>

**Parameters:**
- `string $substring <p>The substring to look for.</p>`
- `bool $caseSensitive [optional] <p>Whether or not to enforce case-sensitivity. Default: true</p>`

**Return:**
- `bool <p>Whether or not $str ends with $substring.</p>`

--------

## endsWithAny(string[] $substrings, bool $caseSensitive): bool
<a href="#class-methods">↑</a>
Returns true if the string ends with any of $substrings, false otherwise.

By default, the comparison is case-sensitive, but can be made insensitive
by setting $caseSensitive to false.

EXAMPLE: <code>
s('fòôbàř')->endsWithAny(['bàř', 'baz'], true); // true
</code>

**Parameters:**
- `array<array-key, string> $substrings <p>Substrings to look for.</p>`
- `bool $caseSensitive [optional] <p>Whether or not to enforce case-sensitivity. Default: true</p>`

**Return:**
- `bool <p>Whether or not $str ends with $substring.</p>`

--------

## ensureLeft(string $substring): static
<a href="#class-methods">↑</a>
Ensures that the string begins with $substring. If it doesn't, it's
prepended.

EXAMPLE: <code>
s('foobar')->ensureLeft('http://'); // 'http://foobar'
</code>

**Parameters:**
- `string $substring <p>The substring to add if not present.</p>`

**Return:**
- `static <p>Object with its $str prefixed by the $substring.</p>`

--------

## ensureRight(string $substring): static
<a href="#class-methods">↑</a>
Ensures that the string ends with $substring. If it doesn't, it's appended.

EXAMPLE: <code>
s('foobar')->ensureRight('.com'); // 'foobar.com'
</code>

**Parameters:**
- `string $substring <p>The substring to add if not present.</p>`

**Return:**
- `static <p>Object with its $str suffixed by the $substring.</p>`

--------

## escape(): static
<a href="#class-methods">↑</a>
Create a escape html version of the string via "htmlspecialchars()".

EXAMPLE: <code>
s('<∂∆ onerror="alert(xss)">')->escape(); // '&lt;∂∆ onerror=&quot;alert(xss)&quot;&gt;'
</code>

**Parameters:**
__nothing__

**Return:**
- `static`

--------

## explode(string $delimiter, int $limit): array<int,static>
<a href="#class-methods">↑</a>
Split a string by a string.

EXAMPLE: <code>
</code>

**Parameters:**
- `string $delimiter <p>The boundary string</p>`
- `int $limit [optional] <p>The maximum number of elements in the exploded
                       collection.</p>

- If limit is set and positive, the returned collection will contain a maximum of limit elements with the last
element containing the rest of string.
- If the limit parameter is negative, all components except the last -limit are returned.
- If the limit parameter is zero, then this is treated as 1`

**Return:**
- `array<int,static>`

--------

## explodeCollection(string $delimiter, int $limit): \CollectionStringy|static[]
<a href="#class-methods">↑</a>
Split a string by a string.

EXAMPLE: <code>
</code>

**Parameters:**
- `string $delimiter <p>The boundary string</p>`
- `int $limit [optional] <p>The maximum number of elements in the exploded
                       collection.</p>

- If limit is set and positive, the returned collection will contain a maximum of limit elements with the last
element containing the rest of string.
- If the limit parameter is negative, all components except the last -limit are returned.
- If the limit parameter is zero, then this is treated as 1`

**Return:**
- `\CollectionStringy|static[] <p>An collection of Stringy objects.</p>`

--------

## extractText(string $search, int|null $length, string $replacerForSkippedText): static
<a href="#class-methods">↑</a>
Create an extract from a sentence, so if the search-string was found, it try to centered in the output.

EXAMPLE: <code>
$sentence = 'This is only a Fork of Stringy, take a look at the new features.';
s($sentence)->extractText('Stringy'); // '...Fork of Stringy...'
</code>

**Parameters:**
- `string $search`
- `int|null $length [optional] <p>Default: null === text->length / 2</p>`
- `string $replacerForSkippedText [optional] <p>Default: …</p>`

**Return:**
- `static`

--------

## first(int $n): static
<a href="#class-methods">↑</a>
Returns the first $n characters of the string.

EXAMPLE: <code>
s('fòôbàř')->first(3); // 'fòô'
</code>

**Parameters:**
- `int $n <p>Number of characters to retrieve from the start.</p>`

**Return:**
- `static <p>Object with its $str being the first $n chars.</p>`

--------

## format(mixed $args): static
<a href="#class-methods">↑</a>
Return a formatted string via sprintf + named parameters via array syntax.

<p>
<br>
It will use "sprintf()" so you can use e.g.:
<br>
<br><pre>s('There are %d monkeys in the %s')->format(5, 'tree');</pre>
<br>
<br><pre>s('There are %2$d monkeys in the %1$s')->format('tree', 5);</pre>
<br>
<br>
But you can also use named parameter via array syntax e.g.:
<br>
<br><pre>s('There are %:count monkeys in the %:location')->format(['count' => 5, 'location' => 'tree');</pre>
</p>

EXAMPLE: <code>
$input = 'one: %2$d, %1$s: 2, %:text_three: %3$d';
s($input)->format(['text_three' => '%4$s'], 'two', 1, 3, 'three'); // 'One: 1, two: 2, three: 3'
</code>

**Parameters:**
- `mixed ...$args [optional]`

**Return:**
- `static <p>A Stringy object produced according to the formatting string
format.</p>`

--------

## getEncoding(): string
<a href="#class-methods">↑</a>
Returns the encoding used by the Stringy object.

EXAMPLE: <code>
s('fòôbàř', 'UTF-8')->getEncoding(); // 'UTF-8'
</code>

**Parameters:**
__nothing__

**Return:**
- `string <p>The current value of the $encoding property.</p>`

--------

## getIterator(): \ArrayIterator
<a href="#class-methods">↑</a>
Returns a new ArrayIterator, thus implementing the IteratorAggregate
interface. The ArrayIterator's constructor is passed an array of chars
in the multibyte string. This enables the use of foreach with instances
of Stringy\Stringy.

EXAMPLE: <code>
</code>

**Parameters:**
__nothing__

**Return:**
- `\ArrayIterator <p>An iterator for the characters in the string.</p>`

--------

## hardWrap(int $width, string $break): static
<a href="#class-methods">↑</a>
Wrap the string after an exact number of characters.

EXAMPLE: <code>
</code>

**Parameters:**
- `int $width <p>Number of characters at which to wrap.</p>`
- `string $break [optional] <p>Character used to break the string. | Default: "\n"</p>`

**Return:**
- `static`

--------

## hasLowerCase(): bool
<a href="#class-methods">↑</a>
Returns true if the string contains a lower case char, false otherwise

EXAMPLE: <code>
s('fòôbàř')->hasLowerCase(); // true
</code>

**Parameters:**
__nothing__

**Return:**
- `bool <p>Whether or not the string contains a lower case character.</p>`

--------

## hasUpperCase(): bool
<a href="#class-methods">↑</a>
Returns true if the string contains an upper case char, false otherwise.

EXAMPLE: <code>
s('fòôbàř')->hasUpperCase(); // false
</code>

**Parameters:**
__nothing__

**Return:**
- `bool <p>Whether or not the string contains an upper case character.</p>`

--------

## hash(string $algorithm): static
<a href="#class-methods">↑</a>
Generate a hash value (message digest).

EXAMPLE: <code>
</code>

**Parameters:**
- `string $algorithm <p>Name of selected hashing algorithm (i.e. "md5", "sha256", "haval160,4", etc..)</p>`

**Return:**
- `static`

--------

## hexDecode(): static
<a href="#class-methods">↑</a>
Decode the string from hex.

EXAMPLE: <code>
</code>

**Parameters:**
__nothing__

**Return:**
- `static`

--------

## hexEncode(): static
<a href="#class-methods">↑</a>
Encode string to hex.

EXAMPLE: <code>
</code>

**Parameters:**
__nothing__

**Return:**
- `static`

--------

## htmlDecode(int $flags): static
<a href="#class-methods">↑</a>
Convert all HTML entities to their applicable characters.

EXAMPLE: <code>
s('&amp;')->htmlDecode(); // '&'
</code>

**Parameters:**
- `int $flags [optional] <p>
A bitmask of one or more of the following flags, which specify how to handle quotes and
which document type to use. The default is ENT_COMPAT.
<table>
Available <i>flags</i> constants
<tr valign="top">
<td>Constant Name</td>
<td>Description</td>
</tr>
<tr valign="top">
<td><b>ENT_COMPAT</b></td>
<td>Will convert double-quotes and leave single-quotes alone.</td>
</tr>
<tr valign="top">
<td><b>ENT_QUOTES</b></td>
<td>Will convert both double and single quotes.</td>
</tr>
<tr valign="top">
<td><b>ENT_NOQUOTES</b></td>
<td>Will leave both double and single quotes unconverted.</td>
</tr>
<tr valign="top">
<td><b>ENT_HTML401</b></td>
<td>
Handle code as HTML 4.01.
</td>
</tr>
<tr valign="top">
<td><b>ENT_XML1</b></td>
<td>
Handle code as XML 1.
</td>
</tr>
<tr valign="top">
<td><b>ENT_XHTML</b></td>
<td>
Handle code as XHTML.
</td>
</tr>
<tr valign="top">
<td><b>ENT_HTML5</b></td>
<td>
Handle code as HTML 5.
</td>
</tr>
</table>
</p>`

**Return:**
- `static <p>Object with the resulting $str after being html decoded.</p>`

--------

## htmlEncode(int $flags): static
<a href="#class-methods">↑</a>
Convert all applicable characters to HTML entities.

EXAMPLE: <code>
s('&')->htmlEncode(); // '&amp;'
</code>

**Parameters:**
- `int $flags [optional] <p>
A bitmask of one or more of the following flags, which specify how to handle quotes and
which document type to use. The default is ENT_COMPAT.
<table>
Available <i>flags</i> constants
<tr valign="top">
<td>Constant Name</td>
<td>Description</td>
</tr>
<tr valign="top">
<td><b>ENT_COMPAT</b></td>
<td>Will convert double-quotes and leave single-quotes alone.</td>
</tr>
<tr valign="top">
<td><b>ENT_QUOTES</b></td>
<td>Will convert both double and single quotes.</td>
</tr>
<tr valign="top">
<td><b>ENT_NOQUOTES</b></td>
<td>Will leave both double and single quotes unconverted.</td>
</tr>
<tr valign="top">
<td><b>ENT_HTML401</b></td>
<td>
Handle code as HTML 4.01.
</td>
</tr>
<tr valign="top">
<td><b>ENT_XML1</b></td>
<td>
Handle code as XML 1.
</td>
</tr>
<tr valign="top">
<td><b>ENT_XHTML</b></td>
<td>
Handle code as XHTML.
</td>
</tr>
<tr valign="top">
<td><b>ENT_HTML5</b></td>
<td>
Handle code as HTML 5.
</td>
</tr>
</table>
</p>`

**Return:**
- `static <p>Object with the resulting $str after being html encoded.</p>`

--------

## humanize(): static
<a href="#class-methods">↑</a>
Capitalizes the first word of the string, replaces underscores with
spaces, and strips '_id'.

EXAMPLE: <code>
s('author_id')->humanize(); // 'Author'
</code>

**Parameters:**
__nothing__

**Return:**
- `static <p>Object with a humanized $str.</p>`

--------

## in(string $str, bool $caseSensitive): bool
<a href="#class-methods">↑</a>
Determine if the current string exists in another string. By
default, the comparison is case-sensitive, but can be made insensitive
by setting $caseSensitive to false.

EXAMPLE: <code>
</code>

**Parameters:**
- `string $str <p>The string to compare against.</p>`
- `bool $caseSensitive [optional] <p>Whether or not to enforce case-sensitivity. Default: true</p>`

**Return:**
- `bool`

--------

## indexOf(string $needle, int $offset): false|int
<a href="#class-methods">↑</a>
Returns the index of the first occurrence of $needle in the string,
and false if not found. Accepts an optional offset from which to begin
the search.

EXAMPLE: <code>
s('string')->indexOf('ing'); // 3
</code>

**Parameters:**
- `string $needle <p>Substring to look for.</p>`
- `int $offset [optional] <p>Offset from which to search. Default: 0</p>`

**Return:**
- `false|int <p>The occurrence's <strong>index</strong> if found, otherwise <strong>false</strong>.</p>`

--------

## indexOfIgnoreCase(string $needle, int $offset): false|int
<a href="#class-methods">↑</a>
Returns the index of the first occurrence of $needle in the string,
and false if not found. Accepts an optional offset from which to begin
the search.

EXAMPLE: <code>
s('string')->indexOfIgnoreCase('ING'); // 3
</code>

**Parameters:**
- `string $needle <p>Substring to look for.</p>`
- `int $offset [optional] <p>Offset from which to search. Default: 0</p>`

**Return:**
- `false|int <p>The occurrence's <strong>index</strong> if found, otherwise <strong>false</strong>.</p>`

--------

## indexOfLast(string $needle, int $offset): false|int
<a href="#class-methods">↑</a>
Returns the index of the last occurrence of $needle in the string,
and false if not found. Accepts an optional offset from which to begin
the search. Offsets may be negative to count from the last character
in the string.

EXAMPLE: <code>
s('foobarfoo')->indexOfLast('foo'); // 10
</code>

**Parameters:**
- `string $needle <p>Substring to look for.</p>`
- `int $offset [optional] <p>Offset from which to search. Default: 0</p>`

**Return:**
- `false|int <p>The last occurrence's <strong>index</strong> if found, otherwise <strong>false</strong>.</p>`

--------

## indexOfLastIgnoreCase(string $needle, int $offset): false|int
<a href="#class-methods">↑</a>
Returns the index of the last occurrence of $needle in the string,
and false if not found. Accepts an optional offset from which to begin
the search. Offsets may be negative to count from the last character
in the string.

EXAMPLE: <code>
s('fooBarFoo')->indexOfLastIgnoreCase('foo'); // 10
</code>

**Parameters:**
- `string $needle <p>Substring to look for.</p>`
- `int $offset [optional] <p>Offset from which to search. Default: 0</p>`

**Return:**
- `false|int <p>The last occurrence's <strong>index</strong> if found, otherwise <strong>false</strong>.</p>`

--------

## insert(string $substring, int $index): static
<a href="#class-methods">↑</a>
Inserts $substring into the string at the $index provided.

EXAMPLE: <code>
s('fòôbř')->insert('à', 4); // 'fòôbàř'
</code>

**Parameters:**
- `string $substring <p>String to be inserted.</p>`
- `int $index <p>The index at which to insert the substring.</p>`

**Return:**
- `static <p>Object with the resulting $str after the insertion.</p>`

--------

## is(string $pattern): bool
<a href="#class-methods">↑</a>
Returns true if the string contains the $pattern, otherwise false.

WARNING: Asterisks ("*") are translated into (".*") zero-or-more regular
expression wildcards.

EXAMPLE: <code>
s('Foo\\Bar\\Lall')->is('*\\Bar\\*'); // true
</code>

**Parameters:**
- `string $pattern <p>The string or pattern to match against.</p>`

**Return:**
- `bool <p>Whether or not we match the provided pattern.</p>`

--------

## isAlpha(): bool
<a href="#class-methods">↑</a>
Returns true if the string contains only alphabetic chars, false otherwise.

EXAMPLE: <code>
s('丹尼爾')->isAlpha(); // true
</code>

**Parameters:**
__nothing__

**Return:**
- `bool <p>Whether or not $str contains only alphabetic chars.</p>`

--------

## isAlphanumeric(): bool
<a href="#class-methods">↑</a>
Returns true if the string contains only alphabetic and numeric chars, false otherwise.

EXAMPLE: <code>
s('دانيال1')->isAlphanumeric(); // true
</code>

**Parameters:**
__nothing__

**Return:**
- `bool <p>Whether or not $str contains only alphanumeric chars.</p>`

--------

## isBase64(bool $emptyStringIsValid): bool
<a href="#class-methods">↑</a>
Returns true if the string is base64 encoded, false otherwise.

EXAMPLE: <code>
s('Zm9vYmFy')->isBase64(); // true
</code>

**Parameters:**
- `bool $emptyStringIsValid`

**Return:**
- `bool <p>Whether or not $str is base64 encoded.</p>`

--------

## isBlank(): bool
<a href="#class-methods">↑</a>
Returns true if the string contains only whitespace chars, false otherwise.

EXAMPLE: <code>
s("\n\t  \v\f")->isBlank(); // true
</code>

**Parameters:**
__nothing__

**Return:**
- `bool <p>Whether or not $str contains only whitespace characters.</p>`

--------

## isEmail(bool $useExampleDomainCheck, bool $useTypoInDomainCheck, bool $useTemporaryDomainCheck, bool $useDnsCheck): bool
<a href="#class-methods">↑</a>
Returns true if the string contains a valid E-Mail address, false otherwise.

EXAMPLE: <code>
s('lars@moelleken.org')->isEmail(); // true
</code>

**Parameters:**
- `bool $useExampleDomainCheck [optional] <p>Default: false</p>`
- `bool $useTypoInDomainCheck [optional] <p>Default: false</p>`
- `bool $useTemporaryDomainCheck [optional] <p>Default: false</p>`
- `bool $useDnsCheck [optional] <p>Default: false</p>`

**Return:**
- `bool <p>Whether or not $str contains a valid E-Mail address.</p>`

--------

## isEmpty(): bool
<a href="#class-methods">↑</a>
Determine whether the string is considered to be empty.

A variable is considered empty if it does not exist or if its value equals FALSE.

EXAMPLE: <code>
s('')->isEmpty(); // true
</code>

**Parameters:**
__nothing__

**Return:**
- `bool <p>Whether or not $str is empty().</p>`

--------

## isEquals(string|\Stringy $str): bool
<a href="#class-methods">↑</a>
Determine whether the string is equals to $str.

Alias for isEqualsCaseSensitive()

EXAMPLE: <code>
s('foo')->isEquals('foo'); // true
</code>

**Parameters:**
- `Stringy|string ...$str`

**Return:**
- `bool`

--------

## isEqualsCaseInsensitive(float|int|string|\Stringy $str): bool
<a href="#class-methods">↑</a>
Determine whether the string is equals to $str.

EXAMPLE: <code>
</code>

**Parameters:**
- `Stringy|float|int|string ...$str <p>The string to compare.</p>`

**Return:**
- `bool <p>Whether or not $str is equals.</p>`

--------

## isEqualsCaseSensitive(float|int|string|\Stringy $str): bool
<a href="#class-methods">↑</a>
Determine whether the string is equals to $str.

EXAMPLE: <code>
</code>

**Parameters:**
- `Stringy|float|int|string ...$str <p>The string to compare.</p>`

**Return:**
- `bool <p>Whether or not $str is equals.</p>`

--------

## isHexadecimal(): bool
<a href="#class-methods">↑</a>
Returns true if the string contains only hexadecimal chars, false otherwise.

EXAMPLE: <code>
s('A102F')->isHexadecimal(); // true
</code>

**Parameters:**
__nothing__

**Return:**
- `bool <p>Whether or not $str contains only hexadecimal chars.</p>`

--------

## isHtml(): bool
<a href="#class-methods">↑</a>
Returns true if the string contains HTML-Tags, false otherwise.

EXAMPLE: <code>
s('<h1>foo</h1>')->isHtml(); // true
</code>

**Parameters:**
__nothing__

**Return:**
- `bool <p>Whether or not $str contains HTML-Tags.</p>`

--------

## isJson(bool $onlyArrayOrObjectResultsAreValid): bool
<a href="#class-methods">↑</a>
Returns true if the string is JSON, false otherwise. Unlike json_decode
in PHP 5.x, this method is consistent with PHP 7 and other JSON parsers,
in that an empty string is not considered valid JSON.

EXAMPLE: <code>
s('{"foo":"bar"}')->isJson(); // true
</code>

**Parameters:**
- `bool $onlyArrayOrObjectResultsAreValid`

**Return:**
- `bool <p>Whether or not $str is JSON.</p>`

--------

## isLowerCase(): bool
<a href="#class-methods">↑</a>
Returns true if the string contains only lower case chars, false otherwise.

EXAMPLE: <code>
s('fòôbàř')->isLowerCase(); // true
</code>

**Parameters:**
__nothing__

**Return:**
- `bool <p>Whether or not $str contains only lower case characters.</p>`

--------

## isNotEmpty(): bool
<a href="#class-methods">↑</a>
Determine whether the string is considered to be NOT empty.

A variable is considered NOT empty if it does exist or if its value equals TRUE.

EXAMPLE: <code>
s('')->isNotEmpty(); // false
</code>

**Parameters:**
__nothing__

**Return:**
- `bool <p>Whether or not $str is empty().</p>`

--------

## isNumeric(): bool
<a href="#class-methods">↑</a>
Determine if the string is composed of numeric characters.

EXAMPLE: <code>
</code>

**Parameters:**
__nothing__

**Return:**
- `bool`

--------

## isPrintable(): bool
<a href="#class-methods">↑</a>
Determine if the string is composed of printable (non-invisible) characters.

EXAMPLE: <code>
</code>

**Parameters:**
__nothing__

**Return:**
- `bool`

--------

## isPunctuation(): bool
<a href="#class-methods">↑</a>
Determine if the string is composed of punctuation characters.

EXAMPLE: <code>
</code>

**Parameters:**
__nothing__

**Return:**
- `bool`

--------

## isSerialized(): bool
<a href="#class-methods">↑</a>
Returns true if the string is serialized, false otherwise.

EXAMPLE: <code>
s('a:1:{s:3:"foo";s:3:"bar";}')->isSerialized(); // true
</code>

**Parameters:**
__nothing__

**Return:**
- `bool <p>Whether or not $str is serialized.</p>`

--------

## isSimilar(string $str, float $minPercentForSimilarity): bool
<a href="#class-methods">↑</a>
Check if two strings are similar.

EXAMPLE: <code>
</code>

**Parameters:**
- `string $str <p>The string to compare against.</p>`
- `float $minPercentForSimilarity [optional] <p>The percentage of needed similarity. | Default: 80%</p>`

**Return:**
- `bool`

--------

## isUpperCase(): bool
<a href="#class-methods">↑</a>
Returns true if the string contains only lower case chars, false
otherwise.

EXAMPLE: <code>
s('FÒÔBÀŘ')->isUpperCase(); // true
</code>

**Parameters:**
__nothing__

**Return:**
- `bool <p>Whether or not $str contains only lower case characters.</p>`

--------

## isWhitespace(): bool
<a href="#class-methods">↑</a>
Returns true if the string contains only whitespace chars, false otherwise.

EXAMPLE: <code>
</code>

**Parameters:**
__nothing__

**Return:**
- `bool <p>Whether or not $str contains only whitespace characters.</p>`

--------

## jsonSerialize(): string
<a href="#class-methods">↑</a>
Returns value which can be serialized by json_encode().

EXAMPLE: <code>
</code>

**Parameters:**
__nothing__

**Return:**
- `string The current value of the $str property`

--------

## kebabCase(): static
<a href="#class-methods">↑</a>
Convert the string to kebab-case.

EXAMPLE: <code>
</code>

**Parameters:**
__nothing__

**Return:**
- `static`

--------

## last(int $n): static
<a href="#class-methods">↑</a>
Returns the last $n characters of the string.

EXAMPLE: <code>
s('fòôbàř')->last(3); // 'bàř'
</code>

**Parameters:**
- `int $n <p>Number of characters to retrieve from the end.</p>`

**Return:**
- `static <p>Object with its $str being the last $n chars.</p>`

--------

## lastSubstringOf(string $needle, bool $beforeNeedle): static
<a href="#class-methods">↑</a>
Gets the substring after (or before via "$beforeNeedle") the last occurrence of the "$needle".

If no match is found returns new empty Stringy object.

EXAMPLE: <code>
</code>

**Parameters:**
- `string $needle <p>The string to look for.</p>`
- `bool $beforeNeedle [optional] <p>Default: false</p>`

**Return:**
- `static`

--------

## lastSubstringOfIgnoreCase(string $needle, bool $beforeNeedle): static
<a href="#class-methods">↑</a>
Gets the substring after (or before via "$beforeNeedle") the last occurrence of the "$needle".

If no match is found returns new empty Stringy object.

EXAMPLE: <code>
</code>

**Parameters:**
- `string $needle <p>The string to look for.</p>`
- `bool $beforeNeedle [optional] <p>Default: false</p>`

**Return:**
- `static`

--------

## length(): int
<a href="#class-methods">↑</a>
Returns the length of the string.

EXAMPLE: <code>
s('fòôbàř')->length(); // 6
</code>

**Parameters:**
__nothing__

**Return:**
- `int <p>The number of characters in $str given the encoding.</p>`

--------

## lineWrap(int $limit, string $break, bool $add_final_break, string|null $delimiter): static
<a href="#class-methods">↑</a>
Line-Wrap the string after $limit, but also after the next word.

EXAMPLE: <code>
</code>

**Parameters:**
- `int $limit [optional] <p>The column width.</p>`
- `string $break [optional] <p>The line is broken using the optional break parameter.</p>`
- `bool $add_final_break [optional] <p>
If this flag is true, then the method will add a $break at the end
of the result string.
</p>`
- `null|string $delimiter [optional] <p>
You can change the default behavior, where we split the string by newline.
</p>`

**Return:**
- `static`

--------

## lineWrapAfterWord(int $limit, string $break, bool $add_final_break, string|null $delimiter): static
<a href="#class-methods">↑</a>
Line-Wrap the string after $limit, but also after the next word.

EXAMPLE: <code>
</code>

**Parameters:**
- `int $limit [optional] <p>The column width.</p>`
- `string $break [optional] <p>The line is broken using the optional break parameter.</p>`
- `bool $add_final_break [optional] <p>
If this flag is true, then the method will add a $break at the end
of the result string.
</p>`
- `null|string $delimiter [optional] <p>
You can change the default behavior, where we split the string by newline.
</p>`

**Return:**
- `static`

--------

## lines(): static[]
<a href="#class-methods">↑</a>
Splits on newlines and carriage returns, returning an array of Stringy
objects corresponding to the lines in the string.

EXAMPLE: <code>
s("fòô\r\nbàř\n")->lines(); // ['fòô', 'bàř', '']
</code>

**Parameters:**
__nothing__

**Return:**
- `static[] <p>An array of Stringy objects.</p>`

--------

## linesCollection(): \CollectionStringy|static[]
<a href="#class-methods">↑</a>
Splits on newlines and carriage returns, returning an array of Stringy
objects corresponding to the lines in the string.

EXAMPLE: <code>
</code>

**Parameters:**
__nothing__

**Return:**
- `\CollectionStringy|static[] <p>An collection of Stringy objects.</p>`

--------

## longestCommonPrefix(string $otherStr): static
<a href="#class-methods">↑</a>
Returns the longest common prefix between the string and $otherStr.

EXAMPLE: <code>
s('foobar')->longestCommonPrefix('foobaz'); // 'fooba'
</code>

**Parameters:**
- `string $otherStr <p>Second string for comparison.</p>`

**Return:**
- `static <p>Object with its $str being the longest common prefix.</p>`

--------

## longestCommonSubstring(string $otherStr): static
<a href="#class-methods">↑</a>
Returns the longest common substring between the string and $otherStr.

In the case of ties, it returns that which occurs first.

EXAMPLE: <code>
s('foobar')->longestCommonSubstring('boofar'); // 'oo'
</code>

**Parameters:**
- `string $otherStr <p>Second string for comparison.</p>`

**Return:**
- `static <p>Object with its $str being the longest common substring.</p>`

--------

## longestCommonSuffix(string $otherStr): static
<a href="#class-methods">↑</a>
Returns the longest common suffix between the string and $otherStr.

EXAMPLE: <code>
s('fòôbàř')->longestCommonSuffix('fòrbàř'); // 'bàř'
</code>

**Parameters:**
- `string $otherStr <p>Second string for comparison.</p>`

**Return:**
- `static <p>Object with its $str being the longest common suffix.</p>`

--------

## lowerCaseFirst(): static
<a href="#class-methods">↑</a>
Converts the first character of the string to lower case.

EXAMPLE: <code>
s('Σ Foo')->lowerCaseFirst(); // 'σ Foo'
</code>

**Parameters:**
__nothing__

**Return:**
- `static <p>Object with the first character of $str being lower case.</p>`

--------

## matchCaseInsensitive(string|\Stringy $str): bool
<a href="#class-methods">↑</a>
Determine if the string matches another string regardless of case.

Alias for isEqualsCaseInsensitive()

EXAMPLE: <code>
</code>

**Parameters:**
- `Stringy|string ...$str <p>The string to compare against.</p>`

**Return:**
- `bool`

--------

## matchCaseSensitive(string|\Stringy $str): bool
<a href="#class-methods">↑</a>
Determine if the string matches another string.

Alias for isEqualsCaseSensitive()

EXAMPLE: <code>
</code>

**Parameters:**
- `Stringy|string ...$str <p>The string to compare against.</p>`

**Return:**
- `bool`

--------

## md5(): static
<a href="#class-methods">↑</a>
Create a md5 hash from the current string.

**Parameters:**
__nothing__

**Return:**
- `static`

--------

## newLineToHtmlBreak(): static
<a href="#class-methods">↑</a>
Replace all breaks [<br> | \r\n | \r | \n | ...] into "<br>".

EXAMPLE: <code>
</code>

**Parameters:**
__nothing__

**Return:**
- `static`

--------

## nth(int $step, int $offset): static
<a href="#class-methods">↑</a>
Get every nth character of the string.

EXAMPLE: <code>
</code>

**Parameters:**
- `int $step <p>The number of characters to step.</p>`
- `int $offset [optional] <p>The string offset to start at.</p>`

**Return:**
- `static`

--------

## offsetExists(int $offset): bool
<a href="#class-methods">↑</a>
Returns whether or not a character exists at an index. Offsets may be
negative to count from the last character in the string. Implements
part of the ArrayAccess interface.

EXAMPLE: <code>
</code>

**Parameters:**
- `int $offset <p>The index to check.</p>`

**Return:**
- `bool <p>Whether or not the index exists.</p>`

--------

## offsetGet(int $offset): string
<a href="#class-methods">↑</a>
Returns the character at the given index. Offsets may be negative to
count from the last character in the string. Implements part of the
ArrayAccess interface, and throws an OutOfBoundsException if the index
does not exist.

EXAMPLE: <code>
</code>

**Parameters:**
- `int $offset <p>The <strong>index</strong> from which to retrieve the char.</p>`

**Return:**
- `string <p>The character at the specified index.</p>`

--------

## offsetSet(int $offset, mixed $value): void
<a href="#class-methods">↑</a>
Implements part of the ArrayAccess interface, but throws an exception
when called. This maintains the immutability of Stringy objects.

EXAMPLE: <code>
</code>

**Parameters:**
- `int $offset <p>The index of the character.</p>`
- `mixed $value <p>Value to set.</p>`

**Return:**
- `void`

--------

## offsetUnset(int $offset): void
<a href="#class-methods">↑</a>
Implements part of the ArrayAccess interface, but throws an exception
when called. This maintains the immutability of Stringy objects.

EXAMPLE: <code>
</code>

**Parameters:**
- `int $offset <p>The index of the character.</p>`

**Return:**
- `void`

--------

## pad(int $length, string $padStr, string $padType): static
<a href="#class-methods">↑</a>
Pads the string to a given length with $padStr. If length is less than
or equal to the length of the string, no padding takes places. The
default string used for padding is a space, and the default type (one of
'left', 'right', 'both') is 'right'. Throws an InvalidArgumentException
if $padType isn't one of those 3 values.

EXAMPLE: <code>
s('fòôbàř')->pad(9, '-/', 'left'); // '-/-fòôbàř'
</code>

**Parameters:**
- `int $length <p>Desired string length after padding.</p>`
- `string $padStr [optional] <p>String used to pad, defaults to space. Default: ' '</p>`
- `string $padType [optional] <p>One of 'left', 'right', 'both'. Default: 'right'</p>`

**Return:**
- `static <p>Object with a padded $str.</p>`

--------

## padBoth(int $length, string $padStr): static
<a href="#class-methods">↑</a>
Returns a new string of a given length such that both sides of the
string are padded. Alias for pad() with a $padType of 'both'.

EXAMPLE: <code>
s('foo bar')->padBoth(9, ' '); // ' foo bar '
</code>

**Parameters:**
- `int $length <p>Desired string length after padding.</p>`
- `string $padStr [optional] <p>String used to pad, defaults to space. Default: ' '</p>`

**Return:**
- `static <p>String with padding applied.</p>`

--------

## padLeft(int $length, string $padStr): static
<a href="#class-methods">↑</a>
Returns a new string of a given length such that the beginning of the
string is padded. Alias for pad() with a $padType of 'left'.

EXAMPLE: <code>
s('foo bar')->padLeft(9, ' '); // '  foo bar'
</code>

**Parameters:**
- `int $length <p>Desired string length after padding.</p>`
- `string $padStr [optional] <p>String used to pad, defaults to space. Default: ' '</p>`

**Return:**
- `static <p>String with left padding.</p>`

--------

## padRight(int $length, string $padStr): static
<a href="#class-methods">↑</a>
Returns a new string of a given length such that the end of the string
is padded. Alias for pad() with a $padType of 'right'.

EXAMPLE: <code>
s('foo bar')->padRight(10, '_*'); // 'foo bar_*_'
</code>

**Parameters:**
- `int $length <p>Desired string length after padding.</p>`
- `string $padStr [optional] <p>String used to pad, defaults to space. Default: ' '</p>`

**Return:**
- `static <p>String with right padding.</p>`

--------

## pascalCase(): static
<a href="#class-methods">↑</a>
Convert the string to PascalCase.

Alias for studlyCase()

EXAMPLE: <code>
</code>

**Parameters:**
__nothing__

**Return:**
- `static`

--------

## prepend(string $prefix): static
<a href="#class-methods">↑</a>
Returns a new string starting with $prefix.

EXAMPLE: <code>
s('bàř')->prepend('fòô'); // 'fòôbàř'
</code>

**Parameters:**
- `string ...$prefix <p>The string to append.</p>`

**Return:**
- `static <p>Object with appended $prefix.</p>`

--------

## prependStringy(\CollectionStringy|static $prefix): static
<a href="#class-methods">↑</a>
Returns a new string starting with $prefix.

EXAMPLE: <code>
</code>

**Parameters:**
- `CollectionStringy|static ...$prefix <p>The Stringy objects to append.</p>`

**Return:**
- `static <p>Object with appended $prefix.</p>`

--------

## regexReplace(string $pattern, string $replacement, string $options, string $delimiter): static
<a href="#class-methods">↑</a>
Replaces all occurrences of $pattern in $str by $replacement.

EXAMPLE: <code>
s('fòô ')->regexReplace('f[òô]+\s', 'bàř'); // 'bàř'
s('fò')->regexReplace('(ò)', '\\1ô'); // 'fòô'
</code>

**Parameters:**
- `string $pattern <p>The regular expression pattern.</p>`
- `string $replacement <p>The string to replace with.</p>`
- `string $options [optional] <p>Matching conditions to be used.</p>`
- `string $delimiter [optional] <p>Delimiter the the regex. Default: '/'</p>`

**Return:**
- `static <p>Object with the result2ing $str after the replacements.</p>`

--------

## removeHtml(string $allowableTags): static
<a href="#class-methods">↑</a>
Remove html via "strip_tags()" from the string.

EXAMPLE: <code>
s('řàb <ô>òf\', ô<br/>foo <a href="#">lall</a>')->removeHtml('<br><br/>'); // 'řàb òf\', ô<br/>foo lall'
</code>

**Parameters:**
- `string $allowableTags [optional] <p>You can use the optional second parameter to specify tags which should
not be stripped. Default: null
</p>`

**Return:**
- `static`

--------

## removeHtmlBreak(string $replacement): static
<a href="#class-methods">↑</a>
Remove all breaks [<br> | \r\n | \r | \n | ...] from the string.

EXAMPLE: <code>
s('řàb <ô>òf\', ô<br/>foo <a href="#">lall</a>')->removeHtmlBreak(''); // 'řàb <ô>òf\', ô< foo <a href="#">lall</a>'
</code>

**Parameters:**
- `string $replacement [optional] <p>Default is a empty string.</p>`

**Return:**
- `static`

--------

## removeLeft(string $substring): static
<a href="#class-methods">↑</a>
Returns a new string with the prefix $substring removed, if present.

EXAMPLE: <code>
s('fòôbàř')->removeLeft('fòô'); // 'bàř'
</code>

**Parameters:**
- `string $substring <p>The prefix to remove.</p>`

**Return:**
- `static <p>Object having a $str without the prefix $substring.</p>`

--------

## removeRight(string $substring): static
<a href="#class-methods">↑</a>
Returns a new string with the suffix $substring removed, if present.

EXAMPLE: <code>
s('fòôbàř')->removeRight('bàř'); // 'fòô'
</code>

**Parameters:**
- `string $substring <p>The suffix to remove.</p>`

**Return:**
- `static <p>Object having a $str without the suffix $substring.</p>`

--------

## removeXss(): static
<a href="#class-methods">↑</a>
Try to remove all XSS-attacks from the string.

EXAMPLE: <code>
s('<IMG SRC=&#x6A&#x61&#x76&#x61&#x73&#x63&#x72&#x69&#x70&#x74&#x3A&#x61&#x6C&#x65&#x72&#x74&#x28&#x27&#x58&#x53&#x53&#x27&#x29>')->removeXss(); // '<IMG >'
</code>

**Parameters:**
__nothing__

**Return:**
- `static`

--------

## repeat(int $multiplier): static
<a href="#class-methods">↑</a>
Returns a repeated string given a multiplier.

EXAMPLE: <code>
s('α')->repeat(3); // 'ααα'
</code>

**Parameters:**
- `int $multiplier <p>The number of times to repeat the string.</p>`

**Return:**
- `static <p>Object with a repeated str.</p>`

--------

## replace(string $search, string $replacement, bool $caseSensitive): static
<a href="#class-methods">↑</a>
Replaces all occurrences of $search in $str by $replacement.

EXAMPLE: <code>
s('fòô bàř fòô bàř')->replace('fòô ', ''); // 'bàř bàř'
</code>

**Parameters:**
- `string $search <p>The needle to search for.</p>`
- `string $replacement <p>The string to replace with.</p>`
- `bool $caseSensitive [optional] <p>Whether or not to enforce case-sensitivity. Default: true</p>`

**Return:**
- `static <p>Object with the resulting $str after the replacements.</p>`

--------

## replaceAll(string[] $search, string|string[] $replacement, bool $caseSensitive): static
<a href="#class-methods">↑</a>
Replaces all occurrences of $search in $str by $replacement.

EXAMPLE: <code>
s('fòô bàř lall bàř')->replaceAll(['fòÔ ', 'lall'], '', false); // 'bàř bàř'
</code>

**Parameters:**
- `array<array-key, string> $search <p>The elements to search for.</p>`
- `array<array-key, string>|string $replacement <p>The string to replace with.</p>`
- `bool $caseSensitive [optional] <p>Whether or not to enforce case-sensitivity. Default: true</p>`

**Return:**
- `static <p>Object with the resulting $str after the replacements.</p>`

--------

## replaceBeginning(string $search, string $replacement): static
<a href="#class-methods">↑</a>
Replaces all occurrences of $search from the beginning of string with $replacement.

EXAMPLE: <code>
s('fòô bàř fòô bàř')->replaceBeginning('fòô', ''); // ' bàř bàř'
</code>

**Parameters:**
- `string $search <p>The string to search for.</p>`
- `string $replacement <p>The replacement.</p>`

**Return:**
- `static <p>Object with the resulting $str after the replacements.</p>`

--------

## replaceEnding(string $search, string $replacement): static
<a href="#class-methods">↑</a>
Replaces all occurrences of $search from the ending of string with $replacement.

EXAMPLE: <code>
s('fòô bàř fòô bàř')->replaceEnding('bàř', ''); // 'fòô bàř fòô '
</code>

**Parameters:**
- `string $search <p>The string to search for.</p>`
- `string $replacement <p>The replacement.</p>`

**Return:**
- `static <p>Object with the resulting $str after the replacements.</p>`

--------

## replaceFirst(string $search, string $replacement): static
<a href="#class-methods">↑</a>
Replaces first occurrences of $search from the beginning of string with $replacement.

EXAMPLE: <code>
</code>

**Parameters:**
- `string $search <p>The string to search for.</p>`
- `string $replacement <p>The replacement.</p>`

**Return:**
- `static <p>Object with the resulting $str after the replacements.</p>`

--------

## replaceLast(string $search, string $replacement): static
<a href="#class-methods">↑</a>
Replaces last occurrences of $search from the ending of string with $replacement.

EXAMPLE: <code>
</code>

**Parameters:**
- `string $search <p>The string to search for.</p>`
- `string $replacement <p>The replacement.</p>`

**Return:**
- `static <p>Object with the resulting $str after the replacements.</p>`

--------

## reverse(): static
<a href="#class-methods">↑</a>
Returns a reversed string. A multibyte version of strrev().

EXAMPLE: <code>
s('fòôbàř')->reverse(); // 'řàbôòf'
</code>

**Parameters:**
__nothing__

**Return:**
- `static <p>Object with a reversed $str.</p>`

--------

## safeTruncate(int $length, string $substring, bool $ignoreDoNotSplitWordsForOneWord): static
<a href="#class-methods">↑</a>
Truncates the string to a given length, while ensuring that it does not
split words. If $substring is provided, and truncating occurs, the
string is further truncated so that the substring may be appended without
exceeding the desired length.

EXAMPLE: <code>
s('What are your plans today?')->safeTruncate(22, '...'); // 'What are your plans...'
</code>

**Parameters:**
- `int $length <p>Desired length of the truncated string.</p>`
- `string $substring [optional] <p>The substring to append if it can fit. Default: ''</p>`
- `bool $ignoreDoNotSplitWordsForOneWord`

**Return:**
- `static <p>Object with the resulting $str after truncating.</p>`

--------

## setInternalEncoding(string $new_encoding): static
<a href="#class-methods">↑</a>
Set the internal character encoding.

EXAMPLE: <code>
</code>

**Parameters:**
- `string $new_encoding <p>The desired character encoding.</p>`

**Return:**
- `static`

--------

## sha1(): static
<a href="#class-methods">↑</a>
Create a sha1 hash from the current string.

EXAMPLE: <code>
</code>

**Parameters:**
__nothing__

**Return:**
- `static`

--------

## sha256(): static
<a href="#class-methods">↑</a>
Create a sha256 hash from the current string.

EXAMPLE: <code>
</code>

**Parameters:**
__nothing__

**Return:**
- `static`

--------

## sha512(): static
<a href="#class-methods">↑</a>
Create a sha512 hash from the current string.

EXAMPLE: <code>
</code>

**Parameters:**
__nothing__

**Return:**
- `static`

--------

## shortenAfterWord(int $length, string $strAddOn): static
<a href="#class-methods">↑</a>
Shorten the string after $length, but also after the next word.

EXAMPLE: <code>
s('this is a test')->shortenAfterWord(2, '...'); // 'this...'
</code>

**Parameters:**
- `int $length <p>The given length.</p>`
- `string $strAddOn [optional] <p>Default: '…'</p>`

**Return:**
- `static`

--------

## shuffle(): static
<a href="#class-methods">↑</a>
A multibyte string shuffle function. It returns a string with its
characters in random order.

EXAMPLE: <code>
s('fòôbàř')->shuffle(); // 'àôřbòf'
</code>

**Parameters:**
__nothing__

**Return:**
- `static <p>Object with a shuffled $str.</p>`

--------

## similarity(string $str): float
<a href="#class-methods">↑</a>
Calculate the similarity between two strings.

EXAMPLE: <code>
</code>

**Parameters:**
- `string $str <p>The delimiting string.</p>`

**Return:**
- `float`

--------

## slice(int $start, int $end): static
<a href="#class-methods">↑</a>
Returns the substring beginning at $start, and up to, but not including
the index specified by $end. If $end is omitted, the function extracts
the remaining string. If $end is negative, it is computed from the end
of the string.

EXAMPLE: <code>
s('fòôbàř')->slice(3, -1); // 'bà'
</code>

**Parameters:**
- `int $start <p>Initial index from which to begin extraction.</p>`
- `int $end [optional] <p>Index at which to end extraction. Default: null</p>`

**Return:**
- `static <p>Object with its $str being the extracted substring.</p>`

--------

## slugify(string $separator, string $language, array<string,string> $replacements, bool $replace_extra_symbols, bool $use_str_to_lower, bool $use_transliterate): static
<a href="#class-methods">↑</a>
Converts the string into an URL slug. This includes replacing non-ASCII
characters with their closest ASCII equivalents, removing remaining
non-ASCII and non-alphanumeric characters, and replacing whitespace with
$separator. The separator defaults to a single dash, and the string
is also converted to lowercase. The language of the source string can
also be supplied for language-specific transliteration.

EXAMPLE: <code>
s('Using strings like fòô bàř')->slugify(); // 'using-strings-like-foo-bar'
</code>

**Parameters:**
- `string $separator [optional] <p>The string used to replace whitespace.</p>`
- `string $language [optional] <p>Language of the source string.</p>`
- `array<string, string> $replacements [optional] <p>A map of replaceable strings.</p>`
- `bool $replace_extra_symbols [optional]  <p>Add some more replacements e.g. "£" with "
pound ".</p>`
- `bool $use_str_to_lower [optional] <p>Use "string to lower" for the input.</p>`
- `bool $use_transliterate [optional]  <p>Use ASCII::to_transliterate() for unknown
chars.</p>`

**Return:**
- `static <p>Object whose $str has been converted to an URL slug.</p>`

--------

## snakeCase(): static
<a href="#class-methods">↑</a>
Convert the string to snake_case.

EXAMPLE: <code>
</code>

**Parameters:**
__nothing__

**Return:**
- `static`

--------

## snakeize(): static
<a href="#class-methods">↑</a>
Convert a string to snake_case.

EXAMPLE: <code>
s('foo1 Bar')->snakeize(); // 'foo_1_bar'
</code>

**Parameters:**
__nothing__

**Return:**
- `static <p>Object with $str in snake_case.</p>`

--------

## softWrap(int $width, string $break): static
<a href="#class-methods">↑</a>
Wrap the string after the first whitespace character after a given number
of characters.

EXAMPLE: <code>
</code>

**Parameters:**
- `int $width <p>Number of characters at which to wrap.</p>`
- `string $break [optional] <p>Character used to break the string. | Default "\n"</p>`

**Return:**
- `static`

--------

## split(string $pattern, int $limit): static[]
<a href="#class-methods">↑</a>
Splits the string with the provided regular expression, returning an
array of Stringy objects. An optional integer $limit will truncate the
results.

EXAMPLE: <code>
s('foo,bar,baz')->split(',', 2); // ['foo', 'bar']
</code>

**Parameters:**
- `string $pattern <p>The regex with which to split the string.</p>`
- `int $limit [optional] <p>Maximum number of results to return. Default: -1 === no
limit</p>`

**Return:**
- `static[] <p>An array of Stringy objects.</p>`

--------

## splitCollection(string $pattern, int $limit): \CollectionStringy|static[]
<a href="#class-methods">↑</a>
Splits the string with the provided regular expression, returning an
collection of Stringy objects. An optional integer $limit will truncate the
results.

EXAMPLE: <code>
</code>

**Parameters:**
- `string $pattern <p>The regex with which to split the string.</p>`
- `int $limit [optional] <p>Maximum number of results to return. Default: -1 === no
limit</p>`

**Return:**
- `\CollectionStringy|static[] <p>An collection of Stringy objects.</p>`

--------

## startsWith(string $substring, bool $caseSensitive): bool
<a href="#class-methods">↑</a>
Returns true if the string begins with $substring, false otherwise. By
default, the comparison is case-sensitive, but can be made insensitive
by setting $caseSensitive to false.

EXAMPLE: <code>
s('FÒÔbàřbaz')->startsWith('fòôbàř', false); // true
</code>

**Parameters:**
- `string $substring <p>The substring to look for.</p>`
- `bool $caseSensitive [optional] <p>Whether or not to enforce case-sensitivity. Default: true</p>`

**Return:**
- `bool <p>Whether or not $str starts with $substring.</p>`

--------

## startsWithAny(string[] $substrings, bool $caseSensitive): bool
<a href="#class-methods">↑</a>
Returns true if the string begins with any of $substrings, false otherwise.

By default the comparison is case-sensitive, but can be made insensitive by
setting $caseSensitive to false.

EXAMPLE: <code>
s('FÒÔbàřbaz')->startsWithAny(['fòô', 'bàř'], false); // true
</code>

**Parameters:**
- `array<array-key, string> $substrings <p>Substrings to look for.</p>`
- `bool $caseSensitive [optional] <p>Whether or not to enforce case-sensitivity. Default: true</p>`

**Return:**
- `bool <p>Whether or not $str starts with $substring.</p>`

--------

## strip(string|string[] $search): static
<a href="#class-methods">↑</a>
Remove one or more strings from the string.

EXAMPLE: <code>
</code>

**Parameters:**
- `array<array-key, string>|string $search One or more strings to be removed`

**Return:**
- `static`

--------

## stripWhitespace(): static
<a href="#class-methods">↑</a>
Strip all whitespace characters. This includes tabs and newline characters,
as well as multibyte whitespace such as the thin space and ideographic space.

EXAMPLE: <code>
s('   Ο     συγγραφέας  ')->stripWhitespace(); // 'Οσυγγραφέας'
</code>

**Parameters:**
__nothing__

**Return:**
- `static`

--------

## stripeCssMediaQueries(): static
<a href="#class-methods">↑</a>
Remove css media-queries.

EXAMPLE: <code>
s('test @media (min-width:660px){ .des-cla #mv-tiles{width:480px} } test ')->stripeCssMediaQueries(); // 'test  test '
</code>

**Parameters:**
__nothing__

**Return:**
- `static`

--------

## stripeEmptyHtmlTags(): static
<a href="#class-methods">↑</a>
Remove empty html-tag.

EXAMPLE: <code>
s('foo<h1></h1>bar')->stripeEmptyHtmlTags(); // 'foobar'
</code>

**Parameters:**
__nothing__

**Return:**
- `static`

--------

## studlyCase(): static
<a href="#class-methods">↑</a>
Convert the string to StudlyCase.

EXAMPLE: <code>
</code>

**Parameters:**
__nothing__

**Return:**
- `static`

--------

## substr(int $start, int $length): static
<a href="#class-methods">↑</a>
Returns the substring beginning at $start with the specified $length.

It differs from the $this->utf8::substr() function in that providing a $length of
null will return the rest of the string, rather than an empty string.

EXAMPLE: <code>
</code>

**Parameters:**
- `int $start <p>Position of the first character to use.</p>`
- `int $length [optional] <p>Maximum number of characters used. Default: null</p>`

**Return:**
- `static <p>Object with its $str being the substring.</p>`

--------

## substring(int $start, int $length): static
<a href="#class-methods">↑</a>
Return part of the string.

Alias for substr()

EXAMPLE: <code>
s('fòôbàř')->substring(2, 3); // 'ôbà'
</code>

**Parameters:**
- `int $start <p>Starting position of the substring.</p>`
- `int $length [optional] <p>Length of substring.</p>`

**Return:**
- `static`

--------

## substringOf(string $needle, bool $beforeNeedle): static
<a href="#class-methods">↑</a>
Gets the substring after (or before via "$beforeNeedle") the first occurrence of the "$needle".

If no match is found returns new empty Stringy object.

EXAMPLE: <code>
</code>

**Parameters:**
- `string $needle <p>The string to look for.</p>`
- `bool $beforeNeedle [optional] <p>Default: false</p>`

**Return:**
- `static`

--------

## substringOfIgnoreCase(string $needle, bool $beforeNeedle): static
<a href="#class-methods">↑</a>
Gets the substring after (or before via "$beforeNeedle") the first occurrence of the "$needle".

If no match is found returns new empty Stringy object.

EXAMPLE: <code>
</code>

**Parameters:**
- `string $needle <p>The string to look for.</p>`
- `bool $beforeNeedle [optional] <p>Default: false</p>`

**Return:**
- `static`

--------

## surround(string $substring): static
<a href="#class-methods">↑</a>
Surrounds $str with the given substring.

EXAMPLE: <code>
s(' ͜ ')->surround('ʘ'); // 'ʘ ͜ ʘ'
</code>

**Parameters:**
- `string $substring <p>The substring to add to both sides.</P>`

**Return:**
- `static <p>Object whose $str had the substring both prepended and appended.</p>`

--------

## swapCase(): static
<a href="#class-methods">↑</a>
Returns a case swapped version of the string.

EXAMPLE: <code>
s('Ντανιλ')->swapCase(); // 'νΤΑΝΙΛ'
</code>

**Parameters:**
__nothing__

**Return:**
- `static <p>Object whose $str has each character's case swapped.</P>`

--------

## tidy(): static
<a href="#class-methods">↑</a>
Returns a string with smart quotes, ellipsis characters, and dashes from
Windows-1252 (commonly used in Word documents) replaced by their ASCII
equivalents.

EXAMPLE: <code>
s('“I see…”')->tidy(); // '"I see..."'
</code>

**Parameters:**
__nothing__

**Return:**
- `static <p>Object whose $str has those characters removed.</p>`

--------

## titleize(array|string[]|null $ignore, string|null $word_define_chars, string|null $language): static
<a href="#class-methods">↑</a>
Returns a trimmed string with the first letter of each word capitalized.

Also accepts an array, $ignore, allowing you to list words not to be
capitalized.

EXAMPLE: <code>
$ignore = ['at', 'by', 'for', 'in', 'of', 'on', 'out', 'to', 'the'];
s('i like to watch television')->titleize($ignore); // 'I Like to Watch Television'
</code>

**Parameters:**
- `array<array-key, mixed|string>|null $ignore [optional] <p>An array of words not to capitalize or null.
Default: null</p>`
- `null|string $word_define_chars [optional] <p>An string of chars that will be used as whitespace
separator === words.</p>`
- `null|string $language [optional] <p>Language of the source string.</p>`

**Return:**
- `static <p>Object with a titleized $str.</p>`

--------

## titleizeForHumans(string[] $ignore): static
<a href="#class-methods">↑</a>
Returns a trimmed string in proper title case: Also accepts an array, $ignore, allowing you to list words not to be
capitalized.

EXAMPLE: <code>
</code>

Adapted from John Gruber's script.

**Parameters:**
- `array<array-key, string> $ignore <p>An array of words not to capitalize.</p>`

**Return:**
- `static <p>Object with a titleized $str</p>`

--------

## toAscii(string $language, bool $removeUnsupported): static
<a href="#class-methods">↑</a>
Returns an ASCII version of the string. A set of non-ASCII characters are
replaced with their closest ASCII counterparts, and the rest are removed
by default. The language or locale of the source string can be supplied
for language-specific transliteration in any of the following formats:
en, en_GB, or en-GB. For example, passing "de" results in "äöü" mapping
to "aeoeue" rather than "aou" as in other languages.

EXAMPLE: <code>
s('fòôbàř')->toAscii(); // 'foobar'
</code>

**Parameters:**
- `string $language [optional] <p>Language of the source string.</p>`
- `bool $removeUnsupported [optional] <p>Whether or not to remove the
unsupported characters.</p>`

**Return:**
- `static <p>Object whose $str contains only ASCII characters.</p>`

--------

## toBoolean(): bool
<a href="#class-methods">↑</a>
Returns a boolean representation of the given logical string value.

For example, <strong>'true', '1', 'on' and 'yes'</strong> will return true. <strong>'false', '0',
'off', and 'no'</strong> will return false. In all instances, case is ignored.
For other numeric strings, their sign will determine the return value.
In addition, blank strings consisting of only whitespace will return
false. For all other strings, the return value is a result of a
boolean cast.

EXAMPLE: <code>
s('OFF')->toBoolean(); // false
</code>

**Parameters:**
__nothing__

**Return:**
- `bool <p>A boolean value for the string.</p>`

--------

## toLowerCase(bool $tryToKeepStringLength, string|null $lang): static
<a href="#class-methods">↑</a>
Converts all characters in the string to lowercase.

EXAMPLE: <code>
s('FÒÔBÀŘ')->toLowerCase(); // 'fòôbàř'
</code>

**Parameters:**
- `bool $tryToKeepStringLength [optional] <p>true === try to keep the string length: e.g. ẞ -> ß</p>`
- `null|string $lang [optional] <p>Set the language for special cases: az, el, lt, tr</p>`

**Return:**
- `static <p>Object with all characters of $str being lowercase.</p>`

--------

## toSpaces(int $tabLength): static
<a href="#class-methods">↑</a>
Converts each tab in the string to some number of spaces, as defined by
$tabLength. By default, each tab is converted to 4 consecutive spaces.

EXAMPLE: <code>
s(' String speech = "Hi"')->toSpaces(); // '    String speech = "Hi"'
</code>

**Parameters:**
- `int $tabLength [optional] <p>Number of spaces to replace each tab with. Default: 4</p>`

**Return:**
- `static <p>Object whose $str has had tabs switched to spaces.</p>`

--------

## toString(): string
<a href="#class-methods">↑</a>
Return Stringy object as string, but you can also use (string) for automatically casting the object into a
string.

EXAMPLE: <code>
s('fòôbàř')->toString(); // 'fòôbàř'
</code>

**Parameters:**
__nothing__

**Return:**
- `string`

--------

## toTabs(int $tabLength): static
<a href="#class-methods">↑</a>
Converts each occurrence of some consecutive number of spaces, as
defined by $tabLength, to a tab. By default, each 4 consecutive spaces
are converted to a tab.

EXAMPLE: <code>
s('    fòô    bàř')->toTabs(); // '   fòô bàř'
</code>

**Parameters:**
- `int $tabLength [optional] <p>Number of spaces to replace with a tab. Default: 4</p>`

**Return:**
- `static <p>Object whose $str has had spaces switched to tabs.</p>`

--------

## toTitleCase(): static
<a href="#class-methods">↑</a>
Converts the first character of each word in the string to uppercase
and all other chars to lowercase.

EXAMPLE: <code>
s('fòô bàř')->toTitleCase(); // 'Fòô Bàř'
</code>

**Parameters:**
__nothing__

**Return:**
- `static <p>Object with all characters of $str being title-cased.</p>`

--------

## toTransliterate(bool $strict, string $unknown): static
<a href="#class-methods">↑</a>
Returns an ASCII version of the string. A set of non-ASCII characters are
replaced with their closest ASCII counterparts, and the rest are removed
unless instructed otherwise.

EXAMPLE: <code>
</code>

**Parameters:**
- `bool $strict [optional] <p>Use "transliterator_transliterate()" from PHP-Intl | WARNING: bad
performance | Default: false</p>`
- `string $unknown [optional] <p>Character use if character unknown. (default is ?)</p>`

**Return:**
- `static <p>Object whose $str contains only ASCII characters.</p>`

--------

## toUpperCase(bool $tryToKeepStringLength, string|null $lang): static
<a href="#class-methods">↑</a>
Converts all characters in the string to uppercase.

EXAMPLE: <code>
s('fòôbàř')->toUpperCase(); // 'FÒÔBÀŘ'
</code>

**Parameters:**
- `bool $tryToKeepStringLength [optional] <p>true === try to keep the string length: e.g. ẞ -> ß</p>`
- `null|string $lang [optional] <p>Set the language for special cases: az, el, lt, tr</p>`

**Return:**
- `static <p>Object with all characters of $str being uppercase.</p>`

--------

## trim(string $chars): static
<a href="#class-methods">↑</a>
Returns a string with whitespace removed from the start and end of the
string. Supports the removal of unicode whitespace. Accepts an optional
string of characters to strip instead of the defaults.

EXAMPLE: <code>
s('  fòôbàř  ')->trim(); // 'fòôbàř'
</code>

**Parameters:**
- `string $chars [optional] <p>String of characters to strip. Default: null</p>`

**Return:**
- `static <p>Object with a trimmed $str.</p>`

--------

## trimLeft(string $chars): static
<a href="#class-methods">↑</a>
Returns a string with whitespace removed from the start of the string.

Supports the removal of unicode whitespace. Accepts an optional
string of characters to strip instead of the defaults.

EXAMPLE: <code>
s('  fòôbàř  ')->trimLeft(); // 'fòôbàř  '
</code>

**Parameters:**
- `string $chars [optional] <p>Optional string of characters to strip. Default: null</p>`

**Return:**
- `static <p>Object with a trimmed $str.</p>`

--------

## trimRight(string $chars): static
<a href="#class-methods">↑</a>
Returns a string with whitespace removed from the end of the string.

Supports the removal of unicode whitespace. Accepts an optional
string of characters to strip instead of the defaults.

EXAMPLE: <code>
s('  fòôbàř  ')->trimRight(); // '  fòôbàř'
</code>

**Parameters:**
- `string $chars [optional] <p>Optional string of characters to strip. Default: null</p>`

**Return:**
- `static <p>Object with a trimmed $str.</p>`

--------

## truncate(int $length, string $substring): static
<a href="#class-methods">↑</a>
Truncates the string to a given length. If $substring is provided, and
truncating occurs, the string is further truncated so that the substring
may be appended without exceeding the desired length.

EXAMPLE: <code>
s('What are your plans today?')->truncate(19, '...'); // 'What are your pl...'
</code>

**Parameters:**
- `int $length <p>Desired length of the truncated string.</p>`
- `string $substring [optional] <p>The substring to append if it can fit. Default: ''</p>`

**Return:**
- `static <p>Object with the resulting $str after truncating.</p>`

--------

## underscored(): static
<a href="#class-methods">↑</a>
Returns a lowercase and trimmed string separated by underscores.

Underscores are inserted before uppercase characters (with the exception
of the first character of the string), and in place of spaces as well as
dashes.

EXAMPLE: <code>
s('TestUCase')->underscored(); // 'test_u_case'
</code>

**Parameters:**
__nothing__

**Return:**
- `static <p>Object with an underscored $str.</p>`

--------

## upperCamelize(): static
<a href="#class-methods">↑</a>
Returns an UpperCamelCase version of the supplied string. It trims
surrounding spaces, capitalizes letters following digits, spaces, dashes
and underscores, and removes spaces, dashes, underscores.

EXAMPLE: <code>
s('Upper Camel-Case')->upperCamelize(); // 'UpperCamelCase'
</code>

**Parameters:**
__nothing__

**Return:**
- `static <p>Object with $str in UpperCamelCase.</p>`

--------

## upperCaseFirst(): static
<a href="#class-methods">↑</a>
Converts the first character of the supplied string to upper case.

EXAMPLE: <code>
s('σ foo')->upperCaseFirst(); // 'Σ foo'
</code>

**Parameters:**
__nothing__

**Return:**
- `static <p>Object with the first character of $str being upper case.</p>`

--------

## urlDecode(): static
<a href="#class-methods">↑</a>
Simple url-decoding.

e.g:
'test+test' => 'test test'

EXAMPLE: <code>
</code>

**Parameters:**
__nothing__

**Return:**
- `static`

--------

## urlDecodeMulti(): static
<a href="#class-methods">↑</a>
Multi url-decoding + decode HTML entity + fix urlencoded-win1252-chars.

e.g:
'test+test'                     => 'test test'
'D&#252;sseldorf'               => 'Düsseldorf'
'D%FCsseldorf'                  => 'Düsseldorf'
'D&#xFC;sseldorf'               => 'Düsseldorf'
'D%26%23xFC%3Bsseldorf'         => 'Düsseldorf'
'DÃ¼sseldorf'                   => 'Düsseldorf'
'D%C3%BCsseldorf'               => 'Düsseldorf'
'D%C3%83%C2%BCsseldorf'         => 'Düsseldorf'
'D%25C3%2583%25C2%25BCsseldorf' => 'Düsseldorf'

EXAMPLE: <code>
</code>

**Parameters:**
__nothing__

**Return:**
- `static`

--------

## urlDecodeRaw(): static
<a href="#class-methods">↑</a>
Simple url-decoding.

e.g:
'test+test' => 'test+test

EXAMPLE: <code>
</code>

**Parameters:**
__nothing__

**Return:**
- `static`

--------

## urlDecodeRawMulti(): static
<a href="#class-methods">↑</a>
Multi url-decoding + decode HTML entity + fix urlencoded-win1252-chars.

e.g:
'test+test'                     => 'test+test'
'D&#252;sseldorf'               => 'Düsseldorf'
'D%FCsseldorf'                  => 'Düsseldorf'
'D&#xFC;sseldorf'               => 'Düsseldorf'
'D%26%23xFC%3Bsseldorf'         => 'Düsseldorf'
'DÃ¼sseldorf'                   => 'Düsseldorf'
'D%C3%BCsseldorf'               => 'Düsseldorf'
'D%C3%83%C2%BCsseldorf'         => 'Düsseldorf'
'D%25C3%2583%25C2%25BCsseldorf' => 'Düsseldorf'

EXAMPLE: <code>
</code>

**Parameters:**
__nothing__

**Return:**
- `static`

--------

## urlEncode(): static
<a href="#class-methods">↑</a>
Simple url-encoding.

e.g:
'test test' => 'test+test'

EXAMPLE: <code>
</code>

**Parameters:**
__nothing__

**Return:**
- `static`

--------

## urlEncodeRaw(): static
<a href="#class-methods">↑</a>
Simple url-encoding.

e.g:
'test test' => 'test%20test'

EXAMPLE: <code>
</code>

**Parameters:**
__nothing__

**Return:**
- `static`

--------

## urlify(string $separator, string $language, array<string,string> $replacements, bool $strToLower): static
<a href="#class-methods">↑</a>
Converts the string into an URL slug. This includes replacing non-ASCII
characters with their closest ASCII equivalents, removing remaining
non-ASCII and non-alphanumeric characters, and replacing whitespace with
$separator. The separator defaults to a single dash, and the string
is also converted to lowercase.

EXAMPLE: <code>
s('Using strings like fòô bàř - 1$')->urlify(); // 'using-strings-like-foo-bar-1-dollar'
</code>

**Parameters:**
- `string $separator [optional] <p>The string used to replace whitespace. Default: '-'</p>`
- `string $language [optional] <p>The language for the url. Default: 'en'</p>`
- `array<string, string> $replacements [optional] <p>A map of replaceable strings.</p>`
- `bool $strToLower [optional] <p>string to lower. Default: true</p>`

**Return:**
- `static <p>Object whose $str has been converted to an URL slug.</p>`

--------

## utf8ify(): static
<a href="#class-methods">↑</a>
Converts the string into an valid UTF-8 string.

EXAMPLE: <code>
s('DÃ¼sseldorf')->utf8ify(); // 'Düsseldorf'
</code>

**Parameters:**
__nothing__

**Return:**
- `static`

--------

## words(string $char_list, bool $remove_empty_values, int|null $remove_short_values): static[]
<a href="#class-methods">↑</a>
Convert a string into an array of words.

EXAMPLE: <code>
</code>

**Parameters:**
- `string $char_list [optional] <p>Additional chars for the definition of "words".</p>`
- `bool $remove_empty_values [optional] <p>Remove empty values.</p>`
- `int|null $remove_short_values [optional] <p>The min. string length or null to disable</p>`

**Return:**
- `static[]`

--------

## wordsCollection(string $char_list, bool $remove_empty_values, int|null $remove_short_values): \CollectionStringy|static[]
<a href="#class-methods">↑</a>
Convert a string into an collection of words.

EXAMPLE: <code>
S::create('中文空白 oöäü#s')->wordsCollection('#', true)->toStrings(); // ['中文空白', 'oöäü#s']
</code>

**Parameters:**
- `string $char_list [optional] <p>Additional chars for the definition of "words".</p>`
- `bool $remove_empty_values [optional] <p>Remove empty values.</p>`
- `int|null $remove_short_values [optional] <p>The min. string length or null to disable</p>`

**Return:**
- `\CollectionStringy|static[] <p>An collection of Stringy objects.</p>`

--------

## wrap(string $substring): static
<a href="#class-methods">↑</a>
Surrounds $str with the given substring.

EXAMPLE: <code>
</code>

**Parameters:**
- `string $substring <p>The substring to add to both sides.</P>`

**Return:**
- `static <p>Object whose $str had the substring both prepended and appended.</p>`

--------


## Tests

From the project directory, tests can be ran using `phpunit`

## Support

For support and donations please visit [Github](https://github.com/voku/Stringy/) | [Issues](https://github.com/voku/Stringy/issues) | [PayPal](https://paypal.me/moelleken) | [Patreon](https://www.patreon.com/voku).

For status updates and release announcements please visit [Releases](https://github.com/voku/Stringy/releases) | [Twitter](https://twitter.com/suckup_de) | [Patreon](https://www.patreon.com/voku/posts).

For professional support please contact [me](https://about.me/voku).

## Thanks

- Thanks to [GitHub](https://github.com) (Microsoft) for hosting the code and a good infrastructure including Issues-Managment, etc.
- Thanks to [IntelliJ](https://www.jetbrains.com) as they make the best IDEs for PHP and they gave me an open source license for PhpStorm!
- Thanks to [Travis CI](https://travis-ci.com/) for being the most awesome, easiest continous integration tool out there!
- Thanks to [StyleCI](https://styleci.io/) for the simple but powerfull code style check.
- Thanks to [PHPStan](https://github.com/phpstan/phpstan) && [Psalm](https://github.com/vimeo/psalm) for relly great Static analysis tools and for discover bugs in the code!

## License

Released under the MIT License - see `LICENSE.txt` for details.
