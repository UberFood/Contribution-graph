<script>
  import { format, compareAsc, differenceInDays, parseISO} from 'date-fns';
  import axios from 'axios';

  function date_to_array_index(date, today, today_index) {
    return today_index + differenceInDays(parseISO(date), today);
  }

	const days = ['Пн', 'Вт', 'Ср', 'Чт', 'Пт', 'Сб', 'Вс'];
  const today = new Date();
  const today_index = 350 + today.getDay();
  let days_array = Array(357);
  let data;

  axios.get('https://dpg.gg/test/calendar.json')
  .then(function (response) {
    data = response.data;
    console.log(data);
    for (const [key, value] of Object.entries(data)) {
      var index =  date_to_array_index(key, today, today_index);
      days_array[index] = value;
    }
    console.log(days_array);
  })
  .catch(function (error) {
    console.log(error);
  })

</script>

<table>
  {#each days as day}
      <tr>
          {#each Array(51) as week}
            <th>
              <button> </button>
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
