## Code Quality Tests

1. **Main**: Main class is named `Crypt` in the default package (source `Crypt.java`).
2. **Modularity**: Source code uses separate classes for main (Crypt.java), Shift cipher, and Unicode cipher. Partial credit for dividing code into Encrypt.java and Decrypt.java. No point for all code in one class, or putting the Cipher code in the main class (Crypt.java).
3. **No Big String**: Code does **not** try to read entire file into a String, a List of Strings, or StringBuilder!  It should read one block (array) or one line, encrypts it, and write the result.
4. **No String Cat**: Code does **not** use string concatenation for building up cipher text or plain text. No `string += string` or `string += char`.
   ```java
   //WRONG
   String ciphertext = "";
   for(int k=0; k<text.length(); k++) {
       char c = text.charAt(k);   // this is bad, too
       c = /* enncrypt char */;
       ciphertext += c;           // WRONG - string concatenation
   }
   ```
5. **CLI Validates Input**: Crypt prints errors if command line arguments are missing or invalid. Examples:
   * `Crypt -alg`               print error for missing algorithm name (not throw ArrayIndexOutOfBounds exception)
   * `Crypt -mode icecream`     print error for invalid mode
   * `Crypt -in doesntexist`    print error that input file doesn't exist
   * `Crypt -foo`               print error for invalid option -foo
   * `Crypt -key five`          OK to throw NumberFormatException, but error message is better.


## Test Files

| File name | Contents |
|:----------|:---------|
| data      | source for Crypt -data "text" test |
| file1     | spaces and newlines |
| file2     | letters and standard symbols |
| file3     | Thai text on two lines, one English word |
| file4     | 256K English text  |

## Test Script

The file `testcrypt` is a Bash script that will compile the student's code and run tests.

First you need to edit two variables:
```
TESTDIR= directory where the test files (file1, file2, ...) are located
OUTDIR= directory for creating output from student code, such as /tmp
```

To run:
```
# chdir to where their source code is
cd student/pa2/src  
# run the script. You may need to specify path, too.
testcrypt
```

If the student "main" class is named CryptApp.java instead of "Crypt.java",
then specify the class name:
```
testcrypt CryptApp
```

## Test Cases and Expected Results

For each test case below, the script runs the test in the table 
and directs the output to a file (`-out somefile`).  
Then compare the output file to a reference file (expected result).
```
java Crypt -in file2 -alg shift -key 10 -out file2.enc
# compare output to a reference file (file2.shift10)
cmp file2.enc  file2.shift10
```

The tests involving file4 are different: encrypt file4 using the command line args,
then decrypt the output file, and compare the the decrypted file to the original file. 
They should be exactly the same.

for example:
```
java Crypt -alg unicode -key 4999 -mode enc -in file4 -out file4.enc 
java Crypt -alg unicode -key 4999 -mode dec -in file4.enc -out file4.dec 
# use the linux "cmp" program to compare result to the original file
cmp file4 file4.dec
```

In tests of file4, if the program runs for more then 1 second then it counts as failure.
A long run time means the code is concatenating many Strings, which it should not do.

If encrypt/decrypt of file4 fails, please look at the output from `cmp`.
The file is 262,700 bytes long.  If `cmp` reports:
```
differ at byte 262,700
// or
cmp: EOF file4 after byte 262,700
```
then their code is *almost* correct but it corrupting the last char or writing an extra char.
Anusid's code is like this.

I didn't show the `-out filename` in the table below. You can use any output filename.

|Test | Arguments to Crypt                 | Output should be same as | 
|-----|:-----------------------------------|:-------------------------|
|  1  | -in file1 -alg shift -key 10       | file1                    | 
|  2  | -in file2 -alg shift               | file2                    | 
|  3  | -in file2 -alg shift -key 10       | file2.shift10            | 
|  4  | -in file2 -alg shift -key 100      | file2.shift100           | 
|  5  | -data "$data" -alg shift -key 13   | data.shift13             | 
|  6  | -in file3 -alg shift -key 25       | file3.shift25 or file3.shift25.thai | 
|  7  | -in file4 -alg shift -key 24 -out file4.enc  |                | 
|     | -in file4.enc -alg shift -key 24 -mode dec | file4            | 
|  8  | -in file1 -alg unicode -key 999    | file1.unicode999         | 
|  9  | -in file2 -alg unicode             | file2                    | 
| 10  | -in file2 -alg unicode -key 500    | file2.unicode500         | 
| 11  | -in file3 -alg unicode -key 500    | file3.unicode500         | 
| 12  | -in file4 -alg unicode -key 4999 -out file4.enc |             | 
|     | -in file4.enc -alg unicode -key 4999 -mode dec | file4        | 

For Test 6 there are two possible solutions, depending on whether
student code shifts only English letters or both English and Thai letters.
