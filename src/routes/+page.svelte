<script>
  // @ts-nocheck

  import { goto } from "$app/navigation";
  import supabase from "$lib/db";
  import { onMount } from "svelte";

  /**
   * @type {any[] | null}
   */
  var CorrenctionRequest = [];

  onMount(async () => {
    // @ts-ignore
    const { data, error } = await supabase
      .from("Correction")
      .select()
      .order("created_at", { ascending: false });
    console.log("This is the Data");
    console.table(data);
    // @ts-ignore
    CorrenctionRequest = data;
  });

  async function Present(CorrectionID, AttendanceID) {
    // Your code to be executed when the button is clicked
    console.log("Button clicked! Parameter:", CorrectionID, "  ", AttendanceID);

     //correcting in requests
     const { error: error1 } = await supabase
      .from("Correction")
      .update({ Status: true,Attendance: true })
      .eq("id", CorrectionID);

    if (!error1) {
      console.log("Updates in requests");
    } else {
      console.log("Error while in Updating In requests");
      console.log(error1);
    }

    //correcting in real Attendance
    const { error: error2 } = await supabase
      .from("Attendance")
      .update({ status: true })
      .eq("id", AttendanceID);

    if (!error2) {
      console.log("Updates in real Attendance");
    } else {
      console.log("Error while in Updating In Real Attendance");
      console.log(error2);
    }
    window.location.reload()
  }

  async function Absent(CorrectionID, AttendanceID) {
    // Your code to be executed when the button is clicked
    console.log("Button clicked! Parameter:", CorrectionID, "  ", AttendanceID);

     //correcting in requests
     const { error: error1 } = await supabase
      .from("Correction")
      .update({ Status: true,Attendance: false })
      .eq("id", CorrectionID);

    if (!error1) {
      console.log("Updates in requests");
    } else {
      console.log("Error while in Updating In requests");
      console.log(error1);
    }

    //correcting in real Attendance
    const { error: error2 } = await supabase
      .from("Attendance")
      .update({ status: false })
      .eq("id", AttendanceID);

    if (!error2) {
      console.log("Updates in real Attendance");
    } else {
      console.log("Error while in Updating In Real Attendance");
      console.log(error2);
    }
    window.location.reload()
  }
</script>

<center>
  <h1>Home</h1>
  <nav>
    <button on:click={() => goto("/TakeAttendance")}>Take Attendance</button>
    <button on:click={() => goto("/OneStudent")}>One Student View</button>
    <div class="CorrectionData">
      <h2>Notifications</h2>
      <p>Correction Requests</p>
      <table border="2px" style="text-align: center;">
        <thead>
          <td>Class_Id</td>
          <td>Student_Id</td>
          <td>Name</td>
          <td>Date</td>
          <td>Period</td>
          <td>Status</td>
          <td>Attendance</td>
          <td>Action</td>
        </thead>
        {#each CorrenctionRequest as request}
          <tr>
            <td>
              <input
                type="text"
                name="ClassId"
                value={request.Class_Id}
                disabled
              />
            </td>
            <td>
              <input
                type="text"
                name="Std_Id"
                value={request.Std_Id}
                disabled
              />
            </td>
            <td>
              <input type="text" name="name" value={request.name} disabled />
            </td>
            <td>
              <input type="text" name="Date" value={request.date} disabled />
            </td>
            <td>
              <input
                type="text"
                name="Period"
                value={request.Period}
                disabled
              />
            </td>
            <td>
              <input
                type="text"
                name="Status"
                value={request.Status}
                disabled
              />
            </td>
            <td>
              <input
                type="text"
                name="Attendance"
                value={request.Attendance}
                disabled
              />
            </td>
            <td>
              <button
                on:click={() => Present(request.id, request.Attendance_Id)}
                >Present</button
              >
              <button
                on:click={() => Absent(request.id, request.Attendance_Id)}
                >Absent</button
              >
            </td>
          </tr>
        {/each}
      </table>
    </div>
  </nav>
</center>

<style>
  /* Add your CSS styles here */
</style>
