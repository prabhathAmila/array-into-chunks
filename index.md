#How to split an array into chunks of the same size easily in Javascript

In this article, you'll learn to split a Javascript array into chunks with a specified size using different implementations.

1. USING A FOR LOOP AND THE SLICE FUNCTION

Basically, every method will use the slice method in order to split the array, in this case what makes this method different is the for loop.

In case that the array is not uniform, the remaining items will be in an array too, however the size will be less for obvious reasons.

<code>
/**
 * Returns an array with arrays of the given size.
 *
 * @param myArray {Array} array to split
 * @param chunk_size {Integer} Size of every group
 */
function chunkArray(myArray, chunk_size){
    var index = 0;
    var arrayLength = myArray.length;
    var tempArray = [];
    
    for (index = 0; index < arrayLength; index += chunk_size) {
        myChunk = myArray.slice(index, index+chunk_size);
        // Do something if you want with the group
        tempArray.push(myChunk);
    }

    return tempArray;
}
// Split in group of 3 items
var result = chunkArray([1,2,3,4,5,6,7,8], 3);
// Outputs : [ [1,2,3] , [4,5,6] ,[7,8] ]
console.log(result);
</code>

2. USING A FOR LOOP, SLICE AND SET FUNCTION IN THE PROTOTYPE OF ARRAY
You can register custom functions in the prototype of a function, in this case you can create a custom function with the name chunk that accomplishes our goal:

<code>
/**
 * Define the chunk method in the prototype of an array
 * that returns an array with arrays of the given size.
 *
 * @param chunkSize {Integer} Size of every group
 */
Object.defineProperty(Array.prototype, 'chunk', {
    value: function(chunkSize){
        var temporal = [];
        
        for (var i = 0; i < this.length; i+= chunkSize){
            temporal.push(this.slice(i,i+chunkSize));
        }
                
        return temporal;
    }
});
// Split in group of 3 items
var result = [1,2,3,4,5,6,7,8].chunk(3);
// Outputs : [ [1,2,3] , [4,5,6] ,[7,8] ]
console.log(result);
</code>
As you can see, the principle is the same using a for loop and the slice function but instead of use it in a function, is registered in the prototype of the array.

3. USING ARRAY MAP IN THE PROTOTYPE OF ARRAY
The map function calls a provided callback function once for each element in an array, in order, and constructs a new array from the results. The function will return an array with a length defined by the division of the length of the providen array by the size of the chunk. The fill function (as no parameter providen) will fill the created array with undefined and finally every undefined value in the array will be replaced by a new array (the result of slice the providen array with the proper index).

<code>
/**
 * Define the chunk method in the prototype of an array
 * that returns an array with arrays of the given size.
 *
 * @param chunkSize {Integer} Size of every group
 */
Object.defineProperty(Array.prototype, 'chunk', {
    value: function(chunkSize) {
        var that = this;
        return Array(Math.ceil(that.length/chunkSize)).fill().map(function(_,i){
            return that.slice(i*chunkSize,i*chunkSize+chunkSize);
        });
    }
});

// Split in group of 3 items
var result = [1,2,3,4,5,6,7,8].chunk(3);
// Outputs : [ [1,2,3] , [4,5,6] ,[7,8] ]
console.log(result);
</code>

4. USING A WHILE LOOP AND SLICE
In tipical and normal conditions the while loop is slightly faster. However we should be aware that these performance gains are significant for large number of iterations. Therefore, if your array is huge and you want to split in chunks with a low number, you should consider in use the method that uses while to drastically increase the performance.

<code>
/**
 * Returns an array with arrays of the given size.
 *
 * @param myArray {Array} Array to split
 * @param chunkSize {Integer} Size of every group
 */
function chunkArray(myArray, chunk_size){
    var results = [];
    
    while (myArray.length) {
        results.push(myArray.splice(0, chunk_size));
    }
    
    return results;
}

// Split in group of 3 items
var result = chunkArray([1,2,3,4,5,6,7,8], 3);
// Outputs : [ [1,2,3] , [4,5,6] ,[7,8] ]
console.log(result);
</code>

5. USING SLICE AND CONCAT WITHIN A RECURSIVE FUNCTION
In this method the recursion is fairly expensive if we talk about performance and browser resources. Besides, concat function is in some browsers significantly slower than the join method.


<code>
/**
 * Define the chunk method in the prototype of an array
 * that returns an array with arrays of the given size (with a recursive function).
 *
 * @param chunk_size {Integer} Size of every group
 */
Array.prototype.chunk = function (chunk_size) {
    if ( !this.length ) {
        return [];
    }

    return [ this.slice( 0, chunk_size ) ].concat(this.slice(chunk_size).chunk(chunk_size));
};
</code>
Disclaimer: don't use in production environments with huge amount of data.


About performance

Our simple benchmark will be to split an array of 100000 (100K) items (only numbers) into chunks of 3 items/array. This task will be executed 1000 (1K) times in order to provide high accuracy, the values are given in milliseconds.

The benchmark has been executed in a machine with the following specifications:

* Operative system Windows 10 Pro 64-bit
* Chrome 53.0.2785.116 m (64-bit)
* Intel(R) Core(TM) i5-4590 CPU @ 3.30GHz (4 CPUs), ~3.3GHz
* 8192MB RAM

METHOD	TOTAL TIME (MS)	AVERAGE TIME PER TASK (MS)
1 (for loop)	5778.015000000001	5.776805000000013
2 (for loop in prototype)	5681.145	5.679875000000007
3 (array map in prototype)	8855.470000000001	8.854190000000001
4 (while loop)	1468.6650000000002	1.468275000000002
5 (recursive function with slice and concat)	TEST-CRASHES	TEST-CRASHES


* The while loop seems to be the quickest way to split an array into chunks with a high performance in comparison to others.
* A detail to notice in the benchmark, is that the bigger the number of items in every chunk, the quicker the task is executed.
* With the method number 5, the browser crashes so the usage of this method is discouraged for huge amount of data.
Have fun !
