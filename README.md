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
