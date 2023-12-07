<script>
  import { format, compareAsc, differenceInDays, parseISO, subDays, addDays, subMonths} from 'date-fns';
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

    popup.style.top = (rect.top - 290) + 'px';
    popup.style.left = (rect.left - 72) + 'px';

    popup.style.visibility = 'visible';

  }

  function display_hint(event, index) {
    var rect = event.target.getBoundingClientRect();

    var popup = document.getElementById("myPopup");
    popup.innerHTML = "";

    var commits_display = document.createElement('h3');
    var string;
    switch(index) {
      case 0:
        string = "No";
        break;
      case 1:
        string = "1-9";
        break;
      case 2:
        string = "10-19";
        break;
      case 3:
        string = "20-29";
        break;
      case 4:
        string = "30+";
        break;
    }
    commits_display.innerHTML = string + " contributions";

    popup.appendChild(commits_display);

    popup.style.top = (rect.top - 290) + 'px';
    popup.style.left = (rect.left - 72) + 'px';

    popup.style.visibility = 'visible';

  }

	const days = ['Пн', 'Вт', 'Ср', 'Чт', 'Пт', 'Сб', 'Вс'];
  const colors = ["light-gray", "#93deff", "#53a8b6", "#5585b5", "#00204a"];
  let weeks = [];
  weeks.length = 51;
  weeks.fill(0);
  let months = [];
  months.length = 13;
  months.fill(0);
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
          cell.style.backgroundColor = colors[0];
      } else if (days_array[j] < 10) {
          cell.style.backgroundColor = colors[1];
      } else if (days_array[j] < 20) {
          cell.style.backgroundColor = colors[2];
      } else if (days_array[j] < 30) {
          cell.style.backgroundColor = colors[3];
      } else {
          cell.style.backgroundColor = colors[4];
      }
    }

  })
  .catch(function (error) {
    console.log(error);
  })

</script>

<div class="vertical">

<table class="month-table">
  <tr>
    {#each months as month, i}
      <th class="month"> {format(subMonths(today, 12-i), "MMM")} </th>
    {/each}
  </tr>
</table>

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

<div class="horizontal">
  <h4>Меньше </h4>

  <table>
  <tr>
  {#each colors as color, i}
    <th>
      <button class='calendar_element' style="background-color: {colors[i]}" on:click={(event) => display_hint(event, i)} > </button>
    </th>
  {/each}
  </tr>
  </table>

  <h4>Больше </h4>
</div>

</div>

<div class="popup" >
  <span class="popuptext" id="myPopup">A Simple Popup!</span>
</div>

<style>

  .month-table {
    width: 1675px;
    margin-left: 50px;
  }

  .month {
    width: 135px;
  }

	.calendar_element {
    aspect-ratio : 1 / 1;
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

.vertical {
  display: flex;
  flex-direction: column;
}

table {
  border-spacing: 0px;
}

ul {
  list-style: none;
  margin: 1px;
}

li {
  display: block;
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
