<script>
    // @ts-nocheck
  
    import supabase from "$lib/db";
    //import { onMount } from "svelte/types/runtime/internal/lifecycle";
    import { onMount } from "svelte";
    import { goto } from "$app/navigation";
  
    let applyFilter = false;
    let students = [];
    let uniqueStudents = [];
    let StudentsList = [];
  
    let department;
    let grade_level;
  
    onMount(async () => {
      const { data, error } = await supabase.from("students").select();
      students = data;
      // console.table(students);
      console.log(data);
  
      uniqueStudents = Array.from(
        new Set(
          students.map((student) =>
            JSON.stringify({
              department: student.department,
              grade_level: student.grade_level,
            })
          )
        )
      ).map((stringified) => JSON.parse(stringified));
    });
  
    
  
    onMount(() => {
      if (typeof document !== "undefined") {
        let SearhBTN = document.getElementById("SearchBTN");
        // do something with SearhBTN
        SearhBTN.addEventListener("click", async () => {
          department = document.getElementById("department").value;
          grade_level = document.getElementById("grade").value;
  
          const { data, error } = await supabase
            .from("students")
            .select()
            .filter("department", "eq", `${department}`)
            .filter("grade_level", "eq", `${grade_level}`);
          StudentsList = data;
          console.table(StudentsList);
        });
      }
    });
  
    async function handleSubmit(event) {
      event.preventDefault();
      const form = event.target;
      const formData = new FormData(event.target);
      const data = Object.fromEntries(formData.entries());
      const selectedStudents = [];
      let StdStatus = false;
  
      for (var i = 0; i < StudentsList.length; i++) {
        var date = document.getElementById("date").value;
        var period = document.getElementById("period").value;
        var StdId = StudentsList[i].id;
        var StdName = document.getElementById(`name_${StdId}`).value;
        var Class_Id = document.getElementById(`Class_Id_${StdId}`).value;
  
        console.log(document.getElementById(`Status_${StdId}`).value);
  
        if (document.getElementById(`Status_${StdId}`).checked) {
          StdStatus = true;
        } else {
          StdStatus = false;
        }
  
        var StdObj = {
          Class_Id: Class_Id,
          Stud_Id: StdId,
          date: date,
          period: period,
          status: StdStatus,
          name: StdName,
        };
        selectedStudents.push(StdObj);
      }
  
      console.log(selectedStudents);
  
      const { datas, error } = await supabase
        .from("Attendance")
        .insert(selectedStudents);
      if (error) {
        console.log(Error);
      } else {
        console.log(datas);
      }
      console.table(selectedStudents);
    }
  </script>
  
  <center>
    <button on:click={() => goto("/")}>Back Home</button>
    <h1>Mark Attendance</h1>
    <p>if Present :-: <input type="checkbox" name="" checked disabled id="" /></p>
    <p>if Absent :-: <input type="checkbox" name="" disabled id="" /></p>
  
    <p>
      Select Department <select name="" id="department">
        <option value="" selected disabled>department</option>
        {#each uniqueStudents as student}
          <option value={student.department}>{student.department}</option>
        {/each}
      </select>
    </p>
  
    <p>
      Select Grade <select name="" id="grade">
        <option value="" selected disabled>grade</option>
        {#each uniqueStudents as student}
          <option value={student.grade_level}>{student.grade_level}</option>
        {/each}
      </select>
    </p>
  
    <button id="SearchBTN">Search</button>
  
    <form on:submit={handleSubmit}>
      <div style="display: flex; justify-content: center; margin: 20px 0;">
        <center>
          <label for="">date: </label>
          <input type="date" name="date" id="date" />
          <br />
          <label for="">period: </label>
          <input type="number" min="1" max="10" name="period" id="period" />
        </center>
      </div>
  
      <table border="1px" style="text-align: center; padding: 5px;">
        <thead>
          <tr>
            <th>ID</th>
            <th>Name</th>
            <th>Status</th>
          </tr>
        </thead>
        <tbody>
          {#each StudentsList as student, i}
            <tr id={`student_${i}`}>
              <td style="text-align: center; padding: 5px;">
                <input
                  type="text"
                  name={`Stud_Id_${student.id}`}
                  readonly
                  value={student.id}
                />
                <input
                  type="text"
                  id={`Class_Id_${student.id}`}
                  name={`Class_Id_${student.id}`}
                  readonly
                  hidden
                  value={student.Class_Id}
                />
              </td>
              <td style="text-align: center; padding: 5px;">
                <input
                  type="text"
                  id={`name_${student.id}`}
                  name={`name_${student.id}`}
                  readonly
                  value={student.name}
                />
              </td>
              <td style="text-align: center; padding: 5px;">
                <input
                  type="checkbox"
                  id={`Status_${student.id}`}
                  name={`Status_${student.id}`}
                  value="true"
                />
              </td>
            </tr>
          {/each}
        </tbody>
      </table>
  
      <input
        id="submitBtN"
        type="submit"
        style="margin-top: 20px;"
        value="Submit"
      />
    </form>
  </center>
  