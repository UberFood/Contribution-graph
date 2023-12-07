<script>
  import { format, compareAsc, differenceInDays, parseISO, subDays, addDays} from 'date-fns';
  import axios from 'axios';

  function date_to_array_index(date, today, today_index) {
    var index =  today_index + differenceInDays(parseISO(date), today);
    return index;
  }

  function parseWeekday(num) {
    if (num == 0) {
      return 6;
    } else {
      return num - 1;
    }
  }

  function display_info(event, index) {
    var rect = event.target.getBoundingClientRect();

    var popup = document.getElementById("myPopup");
    popup.innerHTML = "";

    var date_display = document.createElement('h4');
    date_display.innerHTML = format(days_date[index], "eeee MMM d',' y");
    var commits_display = document.createElement('h3');
    commits_display.innerHTML = days_array[index] + " contributions";

    popup.appendChild(commits_display);
    popup.appendChild(date_display);

    popup.style.top = (rect.top - 238) + 'px';
    popup.style.left = (rect.left - 72) + 'px';

    popup.style.visibility = 'visible';

  }

	const days = ['Пн', 'Вт', 'Ср', 'Чт', 'Пт', 'Сб', 'Вс'];
  let weeks = [];
  weeks.length = 51;
  weeks.fill(0);
  const today = new Date();
  const today_index = 350 + parseWeekday(today.getDay());
  console.log(today_index);
  let days_array = [];
  days_array.length = 357;
  days_array.fill(0);

  let days_date = [];
  for (let i = 0; i < today_index; i++) {
    days_date[i] = subDays(today, today_index - i);
  }

  days_date[today_index] = today;
  for (let i = today_index + 1; i < 357; i++) {
    days_date[i] = addDays(today, i - today_index);
  }

  let data;

  axios.get('https://dpg.gg/test/calendar.json')
  .then(function (response) {
    data = response.data;
    for (const [key, value] of Object.entries(data)) {
      var index =  date_to_array_index(key, today, today_index);
      if (index >= 0) {// otherwise outside of 50 week range
        days_array[index] = value;
      }
    }
    console.log(days_array);
    for (let j = 0; j < days_array.length; j++) {
      var s = 'day_' + j;
      var cell = document.getElementById(s);
      if (days_array[j] == 0) {
          cell.style.backgroundColor = "light-gray";
      } else if (days_array[j] < 10) {
          cell.style.backgroundColor = "#79c2d0";
      } else if (days_array[j] < 20) {
          cell.style.backgroundColor = "#53a8b6";
      } else if (days_array[j] < 30) {
          cell.style.backgroundColor = "#5585b5";
      } else {
          cell.style.backgroundColor = "#00204a";
      }
    }
    //on:click={() => display_info(k*7 + i))}

  })
  .catch(function (error) {
    console.log(error);
  })

</script>

<div class="horizontal">

<ul>
  {#each days as day, i}
    <li> {day} </li>
  {/each}
</ul>

<table>
  {#each days as day, i}
      <tr>
          {#each weeks as week, k}
            <th>
              <button class='calendar_element' id={('day_' + (k*7 + i))}  on:click={(event) => display_info(event, k*7 + i)} > </button>
            </th>
          {/each}
      </tr>
  {/each}

</table>

</div>

<div class="popup" >
  <span class="popuptext" id="myPopup">A Simple Popup!</span>
</div>

<style>
	.calendar_element {
    height: 30px;
    width: 30px;
    border-color: white;
	}

  .calendar_element:hover {
    border-color:black;
  }

  /* Popup container - can be anything you want */
.popup {
  position: relative;
  display: inline-block;
  cursor: pointer;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
}

/* The actual popup */
.popup .popuptext {
  visibility: hidden;
  width: 160px;
  background-color: #000000;
  color: #fff;
  text-align: center;
  border-radius: 2px;
  padding: 2px 0;
  position: absolute;
  z-index: 1;
}

/* Popup arrow */
.popup .popuptext::after {
  content: "";
  position: absolute;
  bottom: 100%;
  left: 50%;
  margin-left: -5px;
  border-width: 5px;
  border-style: solid;
  border-color: transparent transparent #000000 transparent;
}

/* Toggle this class - hide and show the popup */
.popup .show {
  visibility: visible;
  -webkit-animation: fadeIn 1s;
  animation: fadeIn 1s;
}

/* Add animation (fade in the popup) */
@-webkit-keyframes fadeIn {
  from {opacity: 0;}
  to {opacity: 1;}
}

@keyframes fadeIn {
  from {opacity: 0;}
  to {opacity:1 ;}
}

.horizontal {
  display: flex;
  flex-direction: row;
}

table {
  border-spacing: 0px;
}

ul {
  list-style: none;
  margin: 1px;
}

li {
display: inline-block;
  height : 31.6px;
  text-align: center;
  text-color: '#D3D3D3';
}

th {
  padding-top : 0px;
  padding-bottom : 0px;
  margin-top : 0px;
}

</style>
