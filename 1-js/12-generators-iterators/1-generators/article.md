# Generators

Regular functions return only one, single value (or nothing).
Fungsi regular mengembalikan hanya satu, nilai tunggal (atau tidak).

Generators can return ("yield") multiple values, one after another, on-demand. They work great with [iterables](info:iterable), allowing to create data streams with ease.

Generators dapat mengembalikan ("yield") terhadap banyak nilai, satu setelah lainnya, sesuai permintaan. Mereka bekerja dengan baik terhadap [iterables](info:iterable), diperbolahkan untuk membuat data streams dengan mudah.

## Generator functions
## Fungsi Generator

To create a generator, we need a special syntax construct: `function*`, so-called "generator function".
Untuk membuat sebuah generator, kita membutuhkan sebuah konstruksi syntax yang khusus:

It looks like this:
Akan terlihat seperti ini:

```js
function* generateSequence() {
  yield 1;
  yield 2;
  return 3;
}
```

Generator functions behave differently from regular ones. When such function is called, it doesn't run its code. Instead it returns a special object, called "generator object", to manage the execution.
Fungsi generator berperilaku berbeda dari yang biasa. Ketika fungsi tersebut terpanggil, ia tidak akan menjalankan code tersebut. Sebagai gantinya, ia akan mengembalikan sebuah objek yang spesial, bernama "objek ggenerator", untuk mengatur eksekusi.

Here, take a look:
Perhatikan disini:

```js run
function* generateSequence() {
  yield 1;
  yield 2;
  return 3;
}

// "generator function" creates "generator object"
let generator = generateSequence();
*!*
alert(generator); // [object Generator]
*/!*
```

The function code execution hasn't started yet:
Eksekusi fungsi dari code belum dimulai:

![](generateSequence-1.svg)

The main method of a generator is `next()`. When called, it runs the execution until the nearest `yield <value>` statement (`value` can be omitted, then it's `undefined`). Then the function execution pauses, and the yielded `value` is returned to the outer code.
Metode utama dari sebuah generator adalah `next()`. Ketika terpanggil, ia akan menjalankan eksekusi sampai ke statement `yield <value>` yang paling dekat (`value` dapat dihilangkan, lalu akan `undefined`). Kemudian eksekusi dari fungsi jeda, dan `value` yang dihasilkan akan dikembalikan ke kode terluar.

The result of `next()` is always an object with two properties:
- `value`: the yielded value.
- `done`: `true` if the function code has finished, otherwise `false`.
Hasil dari `next()` selalu sebuah objek dengan 2 properti:
- `value`: nilai yang dihasilkan.
- `done`: `true` jika kode dari fungsi berhasil diselesaikan, jika tidak: `false`.

For instance, here we create the generator and get its first yielded value:
Misalnya, disini kita membuat sebuah generator dan mendapatkan nilai pertama yang dihasilkan:

```js run
function* generateSequence() {
  yield 1;
  yield 2;
  return 3;
}

let generator = generateSequence();

*!*
let one = generator.next();
*/!*

alert(JSON.stringify(one)); // {value: 1, done: false}
```

As of now, we got the first value only, and the function execution is on the second line:
Seperti yang sekarang, kita mendapatkan hanya nilai yang pertama, dan eksekusi dari fungsi di baris yang kedua:

![](generateSequence-2.svg)

Let's call `generator.next()` again. It resumes the code execution and returns the next `yield`:
Mari memanggil `generator.next()` lagi. Ia akan melanjutkan eksekusi kode dan mengembalikan `yield` yang selanjutnya:

```js
let two = generator.next();

alert(JSON.stringify(two)); // {value: 2, done: false}
```

![](generateSequence-3.svg)

And, if we call it a third time, the execution reaches the `return` statement that finishes the function:
Dan, jjika kita memanggil itu untuk ketiga kalinya, eksekusi akan mencapai setat yang menyelesaikan fungsinya:

```js
let three = generator.next();

alert(JSON.stringify(three)); // {value: 3, *!*done: true*/!*}
```

![](generateSequence-4.svg)

Now the generator is done. We should see it from `done:true` and process `value:3` as the final result.
Sekarang generator sudah selesai. Kita harus melihatnya dari `done:true` dan proses `value:3` sebagai hasil akhir.

New calls to `generator.next()` don't make sense any more. If we do them, they return the same object: `{done: true}`.
Panggilan baru terhadap `generator.next()` tidak akan masuk akal lagi. Jika kita melakukannya, ia akan mengembalikan objek yang sama: `{done: true}`

```smart header="`function* f(…)` or `function *f(…)`?"
Both syntaxes are correct.
Kedua sintaks adalah benar.

But usually the first syntax is preferred, as the star `*` denotes that it's a generator function, it describes the kind, not the name, so it should stick with the `function` keyword.
Tetapi biasanya sintaks pertama lebih disukai, sebagai bintang `*` menunjukan bahwa ia adalah fungsi generator, ia mendeskripsikan jenis, bukan namanya, jadi ia harus tetap berpegang terhadap kata kunci `function`. 
```

## Generators are iterable
## Generators dapat diulang

As you probably already guessed looking at the `next()` method, generators are [iterable](info:iterable).
Seperti yang mungkin anda tebak pada metode `next()`, generators dapat [diulang](info:iterable) 

We can loop over their values using `for..of`:
Kita dapat memutar ulang nilanya menggunakan `for..of`:

```js run
function* generateSequence() {
  yield 1;
  yield 2;
  return 3;
}

let generator = generateSequence();

for(let value of generator) {
  alert(value); // 1, then 2
}
```

Looks a lot nicer than calling `.next().value`, right?
Terlihat jauh lebih bagus daripada `.next().value`, kan?

...But please note: the example above shows `1`, then `2`, and that's all. It doesn't show `3`!
...Tapi harap diperhatikan: contoh diatas menunjukan `1`, kemudian `2` dan itu saja. Ia tidak menunjukan `3`!

It's because `for..of` iteration ignores the last `value`, when `done: true`. So, if we want all results to be shown by `for..of`, we must return them with `yield`:
Itu karena perulangan `for..of` mengabaikan `value` yang terakhir, ketika `done:true`. Jadi, jika kita ingin melihat seluruh hasil yang akan ditampilkan dari `for..of`, kita harus mengembalikan mereka dengan `yield`:

```js run
function* generateSequence() {
  yield 1;
  yield 2;
*!*
  yield 3;
*/!*
}

let generator = generateSequence();

for(let value of generator) {
  alert(value); // 1, then 2, then 3
}
```

As generators are iterable, we can call all related functionality, e.g. the spread syntax `...`:
Karena generators dapat diulang, kita dapat memanggil semua fungsi yang terkait, misalnya spread syntax `...`:

```js run
function* generateSequence() {
  yield 1;
  yield 2;
  yield 3;
}

let sequence = [0, ...generateSequence()];

alert(sequence); // 0, 1, 2, 3
```

In the code above, `...generateSequence()` turns the iterable generator object into an array of items (read more about the spread syntax in the chapter [](info:rest-parameters-spread#spread-syntax))
Pada kode diatas, `...generateSequence()` merubah objek generator yang dapat diulang kedalam sebuah array dari sebuah item (baca lagi tentang spread syntax di chapter [](info:rest-parameters-spread#spread-syntax))

## Using generators for iterables
## Menggunakan generators untuk diulang

Some time ago, in the chapter [](info:iterable) we created an iterable `range` object that returns values `from..to`.
Beberapa waktu lalu, di bab [](info:iterable) kita membuat sebuah objek `range` yang dapat diulang yang mengembalikan sebuah nilai `from..to`.

Here, let's remember the code:
Disini, mari ingat kodenya:

```js run
let range = {
  from: 1,
  to: 5,

  // for..of range calls this method once in the very beginning
  // for..of range memanggil metode ini sekali di awal
  [Symbol.iterator]() {
    // ...it returns the iterator object:
    // ...ia akan mengembalikan objek iterator:
    // onward, for..of works only with that object, asking it for next values
    // ke depannya, for..of hanya bekerja dengan objek tersebut, dan memintanya untuk nilai yang selanjutnya
    return {
      current: this.from,
      last: this.to,

      // next() is called on each iteration by the for..of loop
      // next akan dipanggil untuk setiap iterasi dari perulangan for..of
      next() {
        // it should return the value as an object {done:..., value :...}
        // ia akan mengembalikan nilai sebagai sebuah objek {done:.., value :...}
        if (this.current <= this.last) {
          return { done: false, value: this.current++ };
        } else {
          return { done: true };
        }
      }
    };
  }
};

// iteration over range returns numbers from range.from to range.to
// iterasi dalam range mengembalikan angka dari range.from ke range
alert([...range]); // 1,2,3,4,5
```

We can use a generator function for iteration by providing it as `Symbol.iterator`.
Kita dapat menggunakan sebuah fungsi generator untuk perulangan dengan menyediakannya sebagai `Symbol.iterator`.

Here's the same `range`, but much more compact:
Di sini rentang yang sama, tapi jauh lebih padat:

```js run
let range = {
  from: 1,
  to: 5,

  *[Symbol.iterator]() { // adalah singkatan untuk [Symbol.iterator]: function*()
    for(let value = this.from; value <= this.to; value++) {
      yield value;
    }
  }
};

alert( [...range] ); // 1,2,3,4,5
```

That works, because `range[Symbol.iterator]()` now returns a generator, and generator methods are exactly what `for..of` expects:
Itu bekerja, karena `range[Symbol.iterator]()` sekarang mengembalikan sebuah generator, dan metode generator adalah persis dengan apa yang `for..of` harapkan.
- it has a `.next()` method
- ia memiliki sebuah metode `.next()`
- that returns values in the form `{value: ..., done: true/false}`
- itu mengembalikan nilai dalam bentuk `{value: ..., done: true/false}`

That's not a coincidence, of course. Generators were added to JavaScript language with iterators in mind, to implement them easily.
Tentunya, itu bukan kebetulan. Generators ditambahkan ke bahasa Javascript dengan memikirkan iterator, untuk mengimplementasikan mereka dengan mudah.

The variant with a generator is much more concise than the original iterable code of `range`, and keeps the same functionality.
Varian dengan generator jauh lebih ringkas daripada perulangan yang original dari kode `range`, dan mempertahankan fungsionalitas yang sama.

```smart header="Generators may generate values forever"
In the examples above we generated finite sequences, but we can also make a generator that yields values forever. For instance, an unending sequence of pseudo-random numbers.
```smart header="Generators dapat menghasilkan nilai selamanya"
Pada contoh diatas kita menghasilkan urutan terbatas, tapi kita dapat juga membuat sebuah generator yang menghasilkan nilai selamanya. Misalnya, sebuah urutan tanpa akhir dari nomor pseudo-random.

That surely would require a `break` (or `return`) in `for..of` over such generator. Otherwise, the loop would repeat forever and hang.
Itu pasti akan membutuhkan sebuah `break` (atau `return`) di `for..of` di atas generator tersebut. Jika tidak, perulangan akan mengulang selamanya dan menggantung.
```

## Generator composition
## Komposisi Generator

Generator composition is a special feature of generators that allows to transparently "embed" generators in each other.
Komposisi generator adalah sebuah fitur spesiap dari generator yang memungkinkan untuk "menyematkan" secara transparant sebuah generators satu sama lain.

For instance, we have a function that generates a sequence of numbers:
Misalnya, kita punya sebuah fungsi yang menghasilkan sebuah urutan angka:

```js
function* generateSequence(start, end) {
  for (let i = start; i <= end; i++) yield i;
}
```

Now we'd like to reuse it to generate a more complex sequence:
Sekarang kita ingin menggunakannya kembali untuk menghasilkan sebuah urutan yang lebih kompleks: 
- first, digits `0..9` (with character codes 48..57),
- pertama, digit `0..9` (dengan kode karakter 48..57)
- followed by uppercase alfabet letters `A..Z` (character codes 65..90)
- di ikuti dengan huruf besar alfabet `A..Z` (kode karakter 65..90)
- followed by lowercase alphabet letters `a..z` (character codes 97..122)
- di ikuti dengan huruf kecil alfabet `a..z` (kode karakter 97..122)

We can use this sequence e.g. to create passwords by selecting characters from it (could add syntax characters as well), but let's generate it first.
Kita dapat menggunakan urutan ini misalnya untuk membuat password dengan memilih karakter dari situ (dapat menambah sintaks karakter juga), tapi mari kita buat dulu. 

In a regular function, to combine results from multiple other functions, we call them, store the results, and then join at the end.
Pada sebuah fungsi biasa, untuk menggabungkan hasil dari beberapa fungsi lainnya, kita memanggil mereka, menyimpan hasilnya, dan pada akhirnya digabungkan.

For generators, there's a special `yield*` syntax to "embed" (compose) one generator into another.
Untuk generators, ada sebuah sintaks `yield` spesial untuk "menanamkan" (menyusun) satu generator dengan lainnya.

The composed generator:
Generator yang tersusun:

```js run
function* generateSequence(start, end) {
  for (let i = start; i <= end; i++) yield i;
}

function* generatePasswordCodes() {

*!*
  // 0..9
  yield* generateSequence(48, 57);

  // A..Z
  yield* generateSequence(65, 90);

  // a..z
  yield* generateSequence(97, 122);
*/!*

}

let str = '';

for(let code of generatePasswordCodes()) {
  str += String.fromCharCode(code);
}

alert(str); // 0..9A..Za..z
```

The `yield*` directive *delegates* the execution to another generator. This term means that `yield* gen` iterates over the generator `gen` and transparently forwards its yields outside. As if the values were yielded by the outer generator.

The result is the same as if we inlined the code from nested generators:

```js run
function* generateSequence(start, end) {
  for (let i = start; i <= end; i++) yield i;
}

function* generateAlphaNum() {

*!*
  // yield* generateSequence(48, 57);
  for (let i = 48; i <= 57; i++) yield i;

  // yield* generateSequence(65, 90);
  for (let i = 65; i <= 90; i++) yield i;

  // yield* generateSequence(97, 122);
  for (let i = 97; i <= 122; i++) yield i;
*/!*

}

let str = '';

for(let code of generateAlphaNum()) {
  str += String.fromCharCode(code);
}

alert(str); // 0..9A..Za..z
```

A generator composition is a natural way to insert a flow of one generator into another. It doesn't use extra memory to store intermediate results.

## "yield" is a two-way street

Until this moment, generators were similar to iterable objects, with a special syntax to generate values. But in fact they are much more powerful and flexible.

That's because `yield` is a two-way street: it not only returns the result to the outside, but also can pass the value inside the generator.

To do so, we should call `generator.next(arg)`, with an argument. That argument becomes the result of `yield`.

Let's see an example:

```js run
function* gen() {
*!*
  // Pass a question to the outer code and wait for an answer
  let result = yield "2 + 2 = ?"; // (*)
*/!*

  alert(result);
}

let generator = gen();

let question = generator.next().value; // <-- yield returns the value

generator.next(4); // --> pass the result into the generator  
```

![](genYield2.svg)

1. The first call `generator.next()` should be always made without an argument (the argument is ignored if passed). It starts the execution and returns the result of the first `yield "2+2=?"`. At this point the generator pauses the execution, while staying on the line `(*)`.
2. Then, as shown at the picture above, the result of `yield` gets into the `question` variable in the calling code.
3. On `generator.next(4)`, the generator resumes, and `4` gets in as the result: `let result = 4`.

Please note, the outer code does not have to immediately call `next(4)`. It may take time. That's not a problem: the generator will wait.

For instance:

```js
// resume the generator after some time
setTimeout(() => generator.next(4), 1000);
```

As we can see, unlike regular functions, a generator and the calling code can exchange results by passing values in `next/yield`.

To make things more obvious, here's another example, with more calls:

```js run
function* gen() {
  let ask1 = yield "2 + 2 = ?";

  alert(ask1); // 4

  let ask2 = yield "3 * 3 = ?"

  alert(ask2); // 9
}

let generator = gen();

alert( generator.next().value ); // "2 + 2 = ?"

alert( generator.next(4).value ); // "3 * 3 = ?"

alert( generator.next(9).done ); // true
```

The execution picture:

![](genYield2-2.svg)

1. The first `.next()` starts the execution... It reaches the first `yield`.
2. The result is returned to the outer code.
3. The second `.next(4)` passes `4` back to the generator as the result of the first `yield`, and resumes the execution.
4. ...It reaches the second `yield`, that becomes the result of the generator call.
5. The third `next(9)` passes `9` into the generator as the result of the second `yield` and resumes the execution that reaches the end of the function, so `done: true`.

It's like a "ping-pong" game. Each `next(value)` (excluding the first one) passes a value into the generator, that becomes the result of the current `yield`, and then gets back the result of the next `yield`.

## generator.throw

As we observed in the examples above, the outer code may pass a value into the generator, as the result of `yield`.

...But it can also initiate (throw) an error there. That's natural, as an error is a kind of result.

To pass an error into a `yield`, we should call `generator.throw(err)`. In that case, the `err` is thrown in the line with that `yield`.

For instance, here the yield of `"2 + 2 = ?"` leads to an error:

```js run
function* gen() {
  try {
    let result = yield "2 + 2 = ?"; // (1)

    alert("The execution does not reach here, because the exception is thrown above");
  } catch(e) {
    alert(e); // shows the error
  }
}

let generator = gen();

let question = generator.next().value;

*!*
generator.throw(new Error("The answer is not found in my database")); // (2)
*/!*
```

The error, thrown into the generator at line `(2)` leads to an exception in line `(1)` with `yield`. In the example above, `try..catch` catches it and shows it.

If we don't catch it, then just like any exception, it "falls out" the generator into the calling code.

The current line of the calling code is the line with `generator.throw`, labelled as `(2)`. So we can catch it here, like this:

```js run
function* generate() {
  let result = yield "2 + 2 = ?"; // Error in this line
}

let generator = generate();

let question = generator.next().value;

*!*
try {
  generator.throw(new Error("The answer is not found in my database"));
} catch(e) {
  alert(e); // shows the error
}
*/!*
```

If we don't catch the error there, then, as usual, it falls through to the outer calling code (if any) and, if uncaught, kills the script.
Jika kita tidak menangkap error disana, kemudian, seperti biasa, ia akan jatuh melalui kode panggilan luar (jika ada) dan, jika tidak tertangkap, akan menghilangkan skripnya.

## Summary
## Rangkuman

- Generators are created by generator functions `function* f(…) {…}`.
- Generator dibuat oleh fungsi generator `function* f(…) {…}`.
- Inside generators (only) there exists a `yield` operator.
- Didalam generator (hanya) ada operator `yield`. 
- The outer code and the generator may exchange results via `next/yield` calls.
- Kode luar dan generator dapat bertukar hasil dengan memanggil `next/yield`

In modern JavaScript, generators are rarely used. But sometimes they come in handy, because the ability of a function to exchange data with the calling code during the execution is quite unique. And, surely, they are great for making iterable objects.
Di Javascript modern, generators jarang digunakan. Tetapi terkadang mereka berguna, karena kemampuan dari fungsi untuk bertukar data dengan memanggil kode selama eksekusi cukup unik.

Also, in the next chapter we'll learn async generators, which are used to read streams of asynchronously generated data (e.g paginated fetches over a network) in `for await ... of` loops.
Juga, di bab selanjutnya kita akan belajar async generators, yang digunakan untuk membaca aliran dari data yang dihasilkan secara asinkron (misalnya pengambilan paginasi melalui jaringan) di perulangan `for await ... of`.

In web-programming we often work with streamed data, so that's another very important use case.
Di pemrograman web kita sering bekerja dengan data yang dialirkan, jadi itu merupakan sebuah kasus penggunaan lain yang sangat penting. 
