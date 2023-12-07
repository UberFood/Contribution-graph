<script>
  import { format, compareAsc, differenceInDays, parseISO} from 'date-fns';
  import axios from 'axios';

  function date_to_array_index(date, today, today_index) {
    var index =  today_index + differenceInDays(parseISO(date), today);
    return index;
  }

	const days = ['Пн', 'Вт', 'Ср', 'Чт', 'Пт', 'Сб', 'Вс'];
  let weeks = [];
  weeks.length = 51;
  weeks.fill(0);
  const today = new Date();
  const today_index = 350 + today.getDay();
  console.log(today_index);
  let days_array = [];
  days_array.length = 357;
  days_array.fill(0);
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
      console.log(cell);
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

  })
  .catch(function (error) {
    console.log(error);
  })

</script>

<table>
  {#each days as day, i}
      <tr>
          {#each weeks as week, k}
            <th>
              <button id={('day_' + (k*7 + i))}> </button>
            </th>
          {/each}
      </tr>
  {/each}

</table>

<style>
	button {
    height:30px;
    width:30px;
	}
</style>
