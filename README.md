# Java port of TLSH (Trend Locality Sensitive Hash)

TLSH is a fuzzy matching library designed by [Trend Micro](http://www.trendmicro.com) (Hosted in [GitHub](https://github.com/trendmicro/tlsh)) 

Given a byte stream with a minimum length of 512 characters (and a minimum amount of randomness), TLSH generates a hash value which can be used for similarity comparisons. Similar objects will have similar hash values which allows for the detection of similar objects by comparing their hash values. Note that the byte stream should have a sufficient amount of complexity. For example, a byte stream of identical bytes will not generate a hash value.

The computed hash is 70 hexadecimal characters long. The first 6 characters are used to capture the information about the file as a whole (length, ...), while the last 64 characters are used to capture information about incremental parts of the file.

JavaScript port (originally designed for use with Node.js) can be found [here](https://github.com/idealista-tech/tlsh-js).

## Installation

You can pull it from the central Maven repositories:

```xml
<dependency>
  <groupId>com.idealista</groupId>
  <artifactId>tlsh</artifactId>
  <version>1.0.0</version>
</dependency>
```

## How it's used

With TLSH mainly you can calculate a hash using supported Strings and compute the difference between two resultant hashes.

### How-To calculate a Hash

To compute a Hash using TLSH, you should do the following:

```java
// Quote extracted from 'The UNIX-HATERS Handbook'
String data = "The best documentation is the UNIX source. After all, this is what the "
				+ "system uses for documentation when it decides what to do next! The "
				+ "manuals paraphrase the source code, often having been written at "
				+ "different times and by different people than who wrote the code. "
				+ "Think of them as guidelines. Sometimes they are more like wishes... "
				+ "Nonetheless, it is all too common to turn to the source and find "
				+ "options and behaviors that are not documented in the manual. Sometimes "
				+ "you find options described in the manual that are unimplemented " 
				+ "and ignored by the source.";
				
TLSH tlsh = new TLSH(data);

String hash = tlsh.hash();
```

The resultant hash will be _6FF02BEF718027B0160B4391212923ED7F1A463D563B1549B86CF62973B197AD2731F8_ as is described in the TLSH unit tests.

### Requirements

The input data must contain:

* At least 512 characters. 
* A certain amount of randomness.

to generate a hash value. In other case an **InsufficientComplexityException** will be thrown.

### How-To compute difference between two hashes

1. You should to create two digests using the Digest Builder with hashes as inputs:

```java
Digest digest1 = new DigestBuilder().withHash("301124198C869A5A4F0F9380A9AE92F2B9278F42089EA34272885F0FB2D34E6911444C").build();
Digest digest2 = new DigestBuilder().withHash("09F05A198CC69A5A4F0F9380A9EE93F2B927CF42089EA74276DC5F0BB2D34E68114448").build();
```

2. You can compute the difference using one Digest against the other one

```java
// Should be equals to digest1.calculateDifference(digest2, true);
int difference = digest2.calculateDifference(digest1, true);
```

The computed difference should be _121_ as is described in Digest unit tests.

**Note:** Computing the difference using a digest against itself should return no difference.

#### How to measure the difference?

* A difference of 0 means the objects are almost identical.
* A difference of 200 or higher means the objects are **very** different.

#### Ignoring the input data length

The difference should be calculated using the file length component or removing it (giving _false_ as second parameter). If an input with a repeating pattern is compared to an input with only a single instance of the pattern, then the difference will be increased if the length is included. Giving a false value to the second parameter, the input data length will be removed from consideration.

## Requirements

The library has been tested with _Apache Maven 3.3.3_ and _JDK 1.6-1.7_. Newer versions of _Apache Maven/JDK_ should work but could also present issues.

## Design choices

We have adopted the original Trend Locality Sensitive Hashing design choices to build this java port.

## TODO

* Make configurables some hardcoded values (e.g. Array of Buckets Size, Pearson Hash Permutation Table, etcetera).
* Allow to improve the accuracy using 134 characters hash strings instead of 70 characters hash strings that are currently supported.
* Complete Data Tests using input data and resulting digests from Trend Micro official repository.

## License 

Read [LICENSE.txt](LICENSE.txt) attached to the project⏎
