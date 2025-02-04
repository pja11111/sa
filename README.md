function bulkDelete() {
    console.log("=== เริ่มต้น bulkDelete ===");

    // ดึง checkbox ที่ถูกติ๊กในทุก task
    const selectedCheckboxes = document.querySelectorAll('.task input[type="checkbox"]:checked');
    console.log("จำนวน checkbox ที่ถูกเลือก:", selectedCheckboxes.length);

    const idsToDelete = [];

    // วนลูปผ่าน checkbox ที่ถูกเลือก
    for (let i = 0; i < selectedCheckboxes.length; i++) {
        const taskId = parseInt(selectedCheckboxes[i].dataset.id);
        console.log(`Checkbox index ${i}: taskId = ${taskId}`);

        // วนลูปตรวจสอบใน tasks เพื่อหา task ที่มี id ตรงกันและมี completed เป็น true
        for (let j = 0; j < tasks.length; j++) {
            console.log(`-- ตรวจสอบ tasks[${j}] (id = ${tasks[j].id}, completed = ${tasks[j].completed}) กับ taskId = ${taskId}`);
            if (tasks[j].id === taskId && tasks[j].completed) {
                console.log(`-- พบ task ที่จะลบ:`, tasks[j]);
                idsToDelete.push(taskId);
                break; // เจอแล้วไม่ต้องวนลูป tasks ต่อในรอบนี้
            }
        }
    }

    console.log("รายชื่อ task IDs ที่จะลบ:", idsToDelete);

    // ถ้าไม่มี task ที่จะลบ
    if (idsToDelete.length === 0) {
        console.log("ไม่มี completed tasks ที่เลือกสำหรับการลบ");
        alert("No completed tasks selected for deletion.");
        return;
    }

    // ยืนยันการลบ
    if (confirm("Are you sure you want to delete the selected completed tasks?")) {
        let newTasks = []; // อาร์เรย์ใหม่สำหรับเก็บ task ที่เหลือ

        // วนลูปผ่านทุก task ใน tasks
        for (let i = 0; i < tasks.length; i++) {
            let shouldDelete = false;

            // ตรวจสอบว่า tasks[i] มี id ที่อยู่ใน idsToDelete หรือไม่
            for (let j = 0; j < idsToDelete.length; j++) {
                if (tasks[i].id === idsToDelete[j]) {
                    shouldDelete = true;
                    console.log(`กำลังลบ tasks[${i}]:`, tasks[i]);
                    break; // เจอแล้วก็หยุดเช็คใน idsToDelete
                }
            }

            // ถ้า task ไม่อยู่ในรายการที่ต้องลบ ให้เก็บไว้ใน newTasks
            if (!shouldDelete) {
                newTasks.push(tasks[i]);
            } else {
                console.log(`tasks[${i}] ถูกลบแล้ว.`);
            }
        }

        console.log("newTasks หลังการลบ:", newTasks);

        // อัปเดต tasks ให้เป็น newTasks ที่ไม่มี task ที่ถูกลบ
        tasks = newTasks;
        saveAndRender(); // บันทึกข้อมูลใหม่ลง localStorage และ render หน้าจอใหม่
        console.log("=== bulkDelete เสร็จสิ้น ===");
    } else {
        console.log("ผู้ใช้ยกเลิกการลบ");
    }
}












function render() {
  // ดึง element ที่มี id "taskList" มาเก็บไว้ในตัวแปร taskList
  const taskList = document.getElementById("taskList");
  
  // เคลียร์เนื้อหาที่แสดงอยู่ใน taskList ก่อน (ลบ task ทั้งหมดที่แสดงอยู่)
  taskList.innerHTML = "";
  
  // วนลูปผ่านทุก task ในอาร์เรย์ tasks
  tasks.forEach((task) => {
    // สร้าง element <li> สำหรับ task แต่ละตัว
    const li = document.createElement("li");

    // กำหนด class ให้กับ <li> เป็น "task"
    // ถ้า task นั้นเสร็จ (completed === true) จะต่อคำว่า " completed" เข้าไปด้วย
    li.className = "task" + (task.completed ? " completed" : "");

    /* สร้าง HTML ภายใน <li> โดยประกอบไปด้วย:
       - Checkbox ที่เก็บ task.id ไว้ใน data-id และติ๊กอยู่ถ้า task.completed เป็น true
       - <span> แสดงชื่อและคำอธิบายของ task
       - ปุ่มลบ (❌) เมื่อคลิกจะเรียกฟังก์ชัน deleteTask() พร้อมส่ง task.id ไป */
    li.innerHTML = `<input type="checkbox" data-id="${task.id}" ${task.completed ? "checked" : ""}>
                    <span>${task.taskname} - ${task.description}</span>
                    <button onclick="deleteTask(${task.id})">❌</button>`;

    // เพิ่ม element <li> ที่สร้างขึ้นเข้าไปใน taskList
    taskList.appendChild(li);
  });
  
  // แสดงข้อมูล tasks ทั้งหมดใน console เพื่อใช้ตรวจสอบหรือดีบัก
  console.log("Tasks:", tasks);
}
