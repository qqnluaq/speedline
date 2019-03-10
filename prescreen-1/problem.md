# Test Data

    [
        {"email": "email1@example.com", "toppings":["Mushrooms","Pepperoni","Peppers"]},
        {"email": "email2@example.com", "toppings":["Cheddar","Garlic","Oregano"]},
        {"email": "email3@example.com", "toppings": ["Bacon","Ham","Pineapple"]},
        {"email": "", "toppings": ["Parmesan","Tomatoes"]},
        {"email": "email4@example.com", "toppings":["Mushrooms","Pepperoni","Peppers"]},
        {"email": "", "toppings": ["Cheddar","Tomatoes"]},
        {"email": "email5@example.com", "toppings": ["Bacon","Ham","Pineapple"]},
        {"email": "email6@example.com", "toppings": ["Beef","Parmesan"]},
        {"email": "", "toppings": ["Onions","Pepperoni"]},
        {"email": "", "toppings": ["Bacon","Ham","Pineapple"]}
    ]

# Algorithm 1

    function printWinners1(inputArray) {
        // Perform a QuickSort on the array.  We provide an anonymous function to do the comparisons.   
        inputArray.sort((a,b)=>{
            // Convert each toppings array to a string and do a string comparison
            return a.toppings.toString().localeCompare(b.toppings.toString());
        });

        let previousEmail = '';
        let previousToppingsAsString = '';
        let previousToppingCount = 0;
        let numberOfSimilarOrders = 0;
        
        // Iterate through the array, with "order" being each item in the array.
        inputArray.map( (order) => {
            let toppingsAsString = order.toppings.toString();
            if (toppingsAsString === previousToppingsAsString) {
                numberOfSimilarOrders++;
            } 
            else {
                if ((numberOfSimilarOrders === 1) && (previousToppingCount ===3) && (previousEmail) !== '') {
                    // Print out the email.
                    console.log(previousEmail);
                }
                previousToppingsAsString = toppingsAsString;
                previousEmail = order.email;
                previousToppingCount = order.toppings.length;
                numberOfSimilarOrders = 1;
            } 
        } );
    }

# Algorithm 2

    function printWinners2(inputArray) {
        let hashTable = new Map();

        // Iterate through the array, with "order" being each item in the array.
        inputArray.map((order) => {
            if ((order.toppings.length === 3) && (order.email !== '')){
                let toppingsAsString = order.toppings.toString();
                let matchingValue = hashTable.get(toppingsAsString);
                if (matchingValue) {
                    // This key was already in the hash table.
                    // matchingValue is a reference to the object in the hash table.
                    matchingValue.duplicate = true;
                } else {
                    // Insert into the hash table, using the toppings as the key and an object containing the email as the value.
                    hashTable.set(toppingsAsString,{
                        email: order.email,
                        duplicate: false
                    });
                } 
            }
        });

        // Iterate through the values in the hash table, with "value" being each value.
        hashTable.forEach((value) => {
            if (!value.duplicate) {
                // Print out the email.
                console.log(value.email);
            }
        }); 
    }

# Questions

1. Describe some data sets that you would use to test any proposed implementation and its accuracy.

    Here are some test data sets that I would use to exercise the implementations, with expected output:

    1. Empty list
        
        `[]`

    1. Malformed input

        `true`

        `0`

        `null`

        `{}`
        
        `[ 1 ]`
        
        `[ [ 1, 2, 3, 4 ] ]`

        `[ { "foo": 1 } ]`

        `[ { "foo": 1 }, { "foo": 2 } ]`

    1. No toppings

        `[ { "email": "foo@bar.com" }, { "email": "bar@foo.com" } ]`

    1. No email

2. Did you identify any bugs in these proposed implementations?


3. Once any small bugs are fixed, will either of these approaches give us the output we're looking for?


4. What are some advantages or disadvantages of these approaches?


5. If we were querying a database instead of working with an in-memory structure, would we use a significantly different algorithm to find the winners? If so, how would you expect that algorithm to differ in performance and why?



# Implementation 1

    function contestAccumulator() {
        var toppingOrderer = {}

        return {
            addOrder: function ( order ) {
                //normalize toppings
                let toppingsKey = order.toppings.sort().join( '-' )

                if ( !toppingOrderer[ toppingsKey ] ) {
                    toppingOrderer[ toppingsKey ] = {
                        email: order.email
                    }
                }
                else {
                    toppingOrderer[ toppingsKey ].duplicated = true
                }
            },

            getWinners: function () {
                return Object.keys( toppingOrderer )
                    .filter( ( key ) => {
                        return !toppingOrderer[ key ].duplicated 
                    } )
                    .map( ( key ) => {
                        return toppingOrderer[ key ].email
                    } )
            }
        }
    }

