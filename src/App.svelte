<script>
  import { onMount, setContext } from "svelte";
  import { writable } from 'svelte/store';

  var previousResultsClient = writable([]);

  let num1 = 0;
  let num2 = 0;
  let result = ''

  //function to update the table to reflect the fetch from the server side array
  const updateTable = async function() {          
    const response = await fetch("http://localhost:3001/getPreviousResults");    //fetch the array
        if (response.ok) {        //if array can be accessed
        const previousResults = await response.json();      // array of previous results 
        
        const tbody = document.querySelector('tbody'); // Get the table body element
        tbody.innerHTML = '';

        previousResults.forEach((result, index) => {        //loop through the fetched array
            const row = document.createElement('tr');       //define row
            const cellIndex = document.createElement('td'); //define index
            cellIndex.textContent = (index + 1);            //set index number to the element in the html

            const cellResult = document.createElement('td');  //define result
            cellResult.textContent = result;

            const deleteButtonCell = document.createElement('td'); // define delete button cell
            const deleteButton = document.createElement('button'); // create delete button
            deleteButton.textContent = 'Delete';        //add text to the button
            deleteButton.addEventListener('click', () => deleteResult(index)); // add event handler to go to the deleteResult function
            deleteButtonCell.appendChild(deleteButton); //map button to the cell

            const editButtonCell = document.createElement('td'); // define delete button cell
            const editButton = document.createElement('button'); // create delete button
            editButton.textContent = 'Edit';        //add text to the button
            editButton.addEventListener('click', () => editResult(index)); // add event handler to go to the deleteResult function
            editButtonCell.appendChild(editButton); //map button to the cell

            row.appendChild(cellIndex);     //add the of the index cell to the row
            row.appendChild(cellResult);    //add the of the result cell to the row
            row.appendChild(deleteButtonCell);    //add the cell for the delete button
            row.appendChild(editButtonCell);      //add the cell for the edit button
            tbody.appendChild(row);           //add the row to thte table
        });
    }
    }
  
    onMount(updateTable);     //update the table
  
    const addition = async function(event) {
        event.preventDefault();

        const num1Input = num1; // Get the value of num1 from Svetle variable
        const num2Input = num2; // Get the value of num2 from Svelte variable

        const json = { operation: 'addition', num1: num1Input, num2: num2Input };
        const body = JSON.stringify(json);

        try {
            const response = await fetch("http://localhost:3001/addition", { // Send the request to the /addition on server side
                method: "POST",
                body: body
            });

            if (!response.ok) {
                throw new Error('Network response was not ok');
            }

            const responseData = await response.json();
            result = responseData.result
            previousResultsClient.set(responseData.previousResults); // Update the store with the array of previous results
            updateTable();
        } catch (error) {
            console.error('Error fetching addition operation:', error);
        }
    };

    const subtraction = async function(event) {
        event.preventDefault();

        const num1Input = num1; // Get the value of num1 from Svetle variable
        const num2Input = num2; // Get the value of num2 from Svelte variable

        const json = { operation: 'subtraction', num1: num1Input, num2: num2Input };
        const body = JSON.stringify(json);

        try {
            const response = await fetch("http://localhost:3001/subtract", { // Send the request to the /subtraction on server side
                method: "POST",
                body: body
            });

            if (!response.ok) {
              throw new Error('Network response was not ok');
            }

            const responseData = await response.json();
            result = responseData.result
            previousResultsClient.set(responseData.previousResults); // Update the store with the array of previous results
            updateTable();
        } catch (error) {
            console.error('Error fetching subtract operation:', error);
        }
    };

    const multiplication = async function(event) {
        event.preventDefault();

        const num1Input = num1; // Get the value of num1 from Svetle variable
        const num2Input = num2; // Get the value of num2 from Svelte variable

        const json = { operation: 'multiplication', num1: num1Input, num2: num2Input };
        const body = JSON.stringify(json);

        try {
            const response = await fetch("http://localhost:3001/multiply", { // Send the request to the /subtraction on server side
                method: "POST",
                body: body
            });

            if (!response.ok) {
              throw new Error('Network response was not ok');
            }

            const responseData = await response.json();
            result = responseData.result
            previousResultsClient.set(responseData.previousResults); // Update the store with the array of previous results
            updateTable();
        } catch (error) {
            console.error('Error fetching mulitplication operation:', error);
        }
    };

    const division = async function(event) {
        event.preventDefault();

        const num1Input = num1; // Get the value of num1 from Svetle variable
        const num2Input = num2; // Get the value of num2 from Svelte variable

        const json = { operation: 'division', num1: num1Input, num2: num2Input };
        const body = JSON.stringify(json);

        try {
            const response = await fetch("http://localhost:3001/divide", { // Send the request to the /subtraction on server side
                method: "POST",
                body: body
            });

            if (!response.ok) {
              throw new Error('Network response was not ok');
            }

            const responseData = await response.json();
            result = responseData.result
            previousResultsClient.set(responseData.previousResults); // Update the store with the array of previous results
            updateTable();
        } catch (error) {
            console.error('Error fetching division operation:', error);
        }
    };

  //function to delete a given entry in the server array with the previous results
  const deleteResult = async function (index) {
    const operation = 'deleteResult'              //define the operation that is referenced on the server side
    const response = await fetch("http://localhost:3001/deleteResult", {           //fetch on the server side
        method: "POST",
        body: JSON.stringify({ operation: operation, index: index })      //pass through, the defined operation and the index in the array that needs to be deleted
    });
    updateTable(); // update the table 
  };

  //function to delete a given entry in the server array with the previous results
  const editResult = async function (index) {
    const newValue = prompt("Enter the new value:"); // Prompt the user for the new value
    const operation = 'editResult'              //define the operation that is referenced on the server side
    const response = await fetch("http://localhost:3001/editResult", {           //fetch on the server side
        method: "POST",
        body: JSON.stringify({ operation: operation, index: index, newValue: newValue })      //pass through, the defined operation and the index in the array that needs to be deleted
    });
    updateTable(); // update the table 
  };

  setContext('previousResults', previousResultsClient);

</script>

<main>
  <h1>Calculator: Addition, Subtraction, Multiplication, and Division</h1>
  <form on:submit|preventDefault={addition}>
    <label for="num1">First Number:</label>
    <input type="number" bind:value={num1} />

    <label for="num2">Second Number:</label>
    <input type="number" bind:value={num2} />
    <button type="submit">Addition</button>
    <button on:click|preventDefault={subtraction}>Subtraction</button>
    <button on:click|preventDefault={multiplication}>Multiplication</button>
    <button on:click|preventDefault={division}>Division</button>
</form>
  <div id = "result">Result: {result}</div>

  <h2>Previous Results:</h2>
  <table>
    <thead>
      <tr>
        <th>Operation #:</th>
        <th>Result:</th>
      </tr>
    </thead>
    <tbody>
      {#each previousResultsClient as result, index}
        <tr>
          <td>{index + 1}</td>
          <td>{result}</td>
          <td>
            <button on:click={deleteResult(index)}>Delete</button>
          </td>
          <td>
            <button on:click={() => editResult(index)}>Edit</button>
          </td>
        </tr>
      {/each}
    </tbody>    
  </table>
</main>

<style>
  @import './styles.css';
</style>