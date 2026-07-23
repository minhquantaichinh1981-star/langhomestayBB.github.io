<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Lặng Homestay - Đặt Phòng Trực Tuyến</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <style>
    .disabled-slot { opacity: 0.4; pointer-events: none; background-color: #f3f4f6 !important; position: relative; }
    .disabled-slot::after { content: 'Đã có khách'; position: absolute; font-size: 10px; color: #dc2626; font-weight: bold; top: 2px; right: 4px; }
  </style>
</head>
<body class="bg-stone-50 text-stone-800 font-sans pb-12">

  <!-- Header -->
  <header class="bg-amber-900 text-amber-50 p-4 shadow-md text-center">
    <h1 class="text-2xl font-bold tracking-wide">LẶNG HOMESTAY</h1>
    <p class="text-xs text-amber-200">Trung tâm Bàu Bàng • Hotline: 0876.666.369</p>
  </header>

  <div class="max-w-2xl mx-auto p-4">
    <!-- Tab Navigation -->
    <div class="flex border-b border-stone-300 mb-6">
      <button onclick="switchTab('booking')" id="tab-booking-btn" class="flex-1 py-2 font-bold text-amber-900 border-b-2 border-amber-900">Khách Đặt Phòng</button>
      <button onclick="switchTab('admin')" id="tab-admin-btn" class="flex-1 py-2 font-bold text-stone-500 border-b-2 border-transparent">Quản Lý Web Admin</button>
    </div>

    <!-- TAB 1: KHÁCH ĐẶT PHÓNG -->
    <div id="tab-booking" class="space-y-6">
      <form id="bookingForm" onsubmit="handleBookingSubmit(event)" class="bg-white p-6 rounded-xl shadow-sm border border-stone-200 space-y-4">
        
        <h2 class="text-lg font-bold text-amber-900 border-b pb-2">1. Chọn Loại Combo & Thời Gian</h2>
        
        <div>
          <label class="block text-sm font-medium mb-1">Loại Hình Đặt Phòng</label>
          <select id="comboType" onchange="calculatePriceAndTimes()" class="w-full border rounded-lg p-2.5 bg-stone-50 focus:ring-2 focus:ring-amber-800 outline-none">
            <option value="COMBO_3H">Combo 3 Giờ (Khung giờ 09:00 - 21:00)</option>
            <option value="COMBO_4H">Combo 4 Giờ (Khung giờ 09:00 - 21:00)</option>
            <option value="COMBO_OVERNIGHT">Combo Qua Đêm (22:00 - 09:00 Sáng hôm sau)</option>
            <option value="COMBO_DAY">Combo Ngày (14:00 - 11:00 Sáng hôm sau)</option>
            <option value="COMBO_2N1D">Combo 2N1Đ (22 Giờ tự chọn)</option>
          </select>
        </div>

        <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
          <div>
            <label class="block text-sm font-medium mb-1">Ngày Nhận Phòng</label>
            <input type="date" id="checkInDate" onchange="calculatePriceAndTimes()" required class="w-full border rounded-lg p-2.5 bg-stone-50">
          </div>
          <div id="hourlyTimePicker">
            <label class="block text-sm font-medium mb-1">Giờ Bắt Đầu (Cho Ca Giờ)</label>
            <input type="time" id="startTime" value="10:00" min="09:00" max="18:00" onchange="calculatePriceAndTimes()" class="w-full border rounded-lg p-2.5 bg-stone-50">
          </div>
        </div>

        <!-- Khung Thời Gian Tính Toán -->
        <div class="bg-amber-50 p-3 rounded-lg text-xs text-amber-900 space-y-1">
          <p>Thời gian nhận: <span id="displayCheckIn" class="font-bold">--</span></p>
          <p>Thời gian trả: <span id="displayCheckOut" class="font-bold">--</span></p>
          <p>Tạm tính chi phí: <span id="displayPrice" class="font-bold text-base text-red-600">0 đ</span></p>
        </div>

        <h2 class="text-lg font-bold text-amber-900 border-b pb-2 pt-2">2. Chọn Phòng (7 Phòng)</h2>
        <div id="roomContainer" class="grid grid-cols-2 gap-3">
          <p class="text-sm text-stone-500 col-span-2">Đang tải danh sách phòng...</p>
        </div>

        <h2 class="text-lg font-bold text-amber-900 border-b pb-2 pt-2">3. Thông Tin Khách Hang & CCCD</h2>
        <div>
          <label class="block text-sm font-medium mb-1">Họ và Tên</label>
          <input type="text" id="fullName" placeholder="Ví dụ: Hồ Hoàng Hiếu" required class="w-full border rounded-lg p-2.5">
        </div>

        <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
          <div>
            <label class="block text-sm font-medium mb-1">Số Điện Thoại</label>
            <input type="tel" id="phone" placeholder="09xxxxxxx" required class="w-full border rounded-lg p-2.5">
          </div>
          <div>
            <label class="block text-sm font-medium mb-1">Địa Chỉ Email (Nhận vé đặt phòng)</label>
            <input type="email" id="email" placeholder="example@gmail.com" required class="w-full border rounded-lg p-2.5">
          </div>
        </div>

        <div class="grid grid-cols-2 gap-3">
          <div>
            <label class="block text-xs font-medium mb-1">Ảnh CCCD Mặt Trước</label>
            <input type="file" id="idFront" accept="image/*" required class="text-xs w-full">
          </div>
          <div>
            <label class="block text-xs font-medium mb-1">Ảnh CCCD Mặt Sau</label>
            <input type="file" id="idBack" accept="image/*" required class="text-xs w-full">
          </div>
        </div>

        <button type="submit" id="btnSubmit" class="w-full bg-amber-800 hover:bg-amber-900 text-white font-bold py-3 rounded-lg shadow transition">
          ĐĂNG KÝ & THANH TOÁN
        </button>
      </form>

      <!-- MODAL THANH TOÁN QR VIETQR -->
      <div id="paymentModal" class="hidden fixed inset-0 bg-black/60 backdrop-blur-sm flex items-center justify-center p-4 z-50">
        <div class="bg-white rounded-2xl p-6 max-w-sm w-full text-center space-y-4 shadow-xl">
          <h3 class="text-lg font-bold text-stone-800">Quét Mã VietQR Thanh Toán</h3>
          
          <!-- Đồng hồ đếm ngược 10 phút -->
          <div class="bg-red-50 text-red-600 p-2 rounded-lg text-sm font-bold">
            Thời gian giữ phòng còn lại: <span id="countdownTimer">10:00</span>
          </div>

          <img id="qrImage" src="" alt="Mã VietQR" class="w-64 h-64 mx-auto border p-2 rounded-lg shadow-inner">

          <div class="text-xs text-left bg-stone-100 p-3 rounded-lg space-y-1 text-stone-700">
            <p><b>Ngân hàng:</b> Techcombank</p>
            <p><b>Chủ TK:</b> HỒ HOÀNG HIẾU</p>
            <p><b>STK:</b> 19022423550011</p>
            <p><b>Số tiền:</b> <span id="qrAmount" class="font-bold text-red-600">0 đ</span></p>
            <p><b>Nội dung chuyển khoản:</b> <span id="qrMemo" class="font-bold text-blue-700 select-all">--</span></p>
          </div>

          <p class="text-xs text-stone-500">Sau khi chuyển khoản, Lễ tân sẽ xác nhận trong giây lát và gửi Email cho bạn!</p>
          <button onclick="closeModal()" class="w-full bg-stone-200 text-stone-700 py-2 rounded-lg font-bold text-sm">Đóng</button>
        </div>
      </div>
    </div>

    <!-- TAB 2: WEB ADMIN DÀNH CHO NHÂN SỰ DUYỆT -->
    <div id="tab-admin" class="hidden space-y-4">
      <div class="bg-white p-4 rounded-xl shadow-sm border border-stone-200 flex gap-2">
        <input type="password" id="adminPin" placeholder="Nhập Mã PIN Admin" class="border rounded-lg p-2 text-sm flex-1">
        <button onclick="loadAdminBookings()" class="bg-amber-800 text-white px-4 py-2 rounded-lg text-sm font-bold">Tải Đơn</button>
      </div>

      <div id="adminList" class="space-y-3">
        <p class="text-sm text-stone-500 text-center">Vui lòng nhập Mã PIN để xem danh sách đơn chờ duyệt.</p>
      </div>
    </div>
  </div>

  <script>
    // THAY LINK SCRIPT ĐÃ DEPLOY CỦA BẠN VÀO ĐÂY:
    const GAS_API_URL = "URL_WEB_APP_GOOGLE_APPS_SCRIPT";

    let selectedRoomId = "";
    let calculatedTotalPrice = 0;
    let checkInStr = "";
    let checkOutStr = "";
    let timerInterval = null;

    // Giá theo bảng giá
    const PRICING = {
      COMBO_3H: { weekday: 189000, weekend: 229000 },
      COMBO_4H: { weekday: 239000, weekend: 259000 },
      COMBO_OVERNIGHT: { weekday: 299000, weekend: 349000 },
      COMBO_DAY: { weekday: 469000, weekend: 499000 },
      COMBO_2N1D: { weekday: 740000, weekend: 799000 }
    };

    window.onload = function() {
      document.getElementById("checkInDate").valueAsDate = new Date();
      calculatePriceAndTimes();
      loadRooms();
    };

    function switchTab(tab) {
      if(tab === 'booking') {
        document.getElementById('tab-booking').classList.remove('hidden');
        document.getElementById('tab-admin').classList.add('hidden');
      } else {
        document.getElementById('tab-booking').classList.add('hidden');
        document.getElementById('tab-admin').classList.remove('hidden');
      }
    }

    function calculatePriceAndTimes() {
      const combo = document.getElementById("comboType").value;
      const dateVal = document.getElementById("checkInDate").value;
      const startTimeVal = document.getElementById("startTime").value;

      if(!dateVal) return;

      const dateObj = new Date(dateVal);
      const dayOfWeek = dateObj.getDay(); // 0: CN, 5: T6, 6: T7
      const isWeekend = (dayOfWeek === 0 || dayOfWeek === 5 || dayOfWeek === 6);

      // Tính Giá
      calculatedTotalPrice = isWeekend ? PRICING[combo].weekend : PRICING[combo].weekday;
      document.getElementById("displayPrice").innerText = calculatedTotalPrice.toLocaleString('vi-VN') + " đ";

      // Tính Khung Giờ
      const checkInDateObj = new Date(dateVal);
      let checkOutDateObj = new Date(dateVal);

      if (combo === "COMBO_3H" || combo === "COMBO_4H") {
        document.getElementById("hourlyTimePicker").style.display = "block";
        const [h, m] = startTimeVal.split(":").map(Number);
        checkInDateObj.setHours(h, m, 0);
        const hoursToAdd = combo === "COMBO_3H" ? 3 : 4;
        checkOutDateObj.setHours(h + hoursToAdd, m, 0);
      } else if (combo === "COMBO_OVERNIGHT") {
        document.getElementById("hourlyTimePicker").style.display = "none";
        checkInDateObj.setHours(22, 0, 0);
        checkOutDateObj.setDate(checkOutDateObj.getDate() + 1);
        checkOutDateObj.setHours(9, 0, 0);
      } else if (combo === "COMBO_DAY") {
        document.getElementById("hourlyTimePicker").style.display = "none";
        checkInDateObj.setHours(14, 0, 0);
        checkOutDateObj.setDate(checkOutDateObj.getDate() + 1);
        checkOutDateObj.setHours(11, 0, 0);
      } else if (combo === "COMBO_2N1D") {
        document.getElementById("hourlyTimePicker").style.display = "none";
        checkInDateObj.setHours(14, 0, 0);
        checkOutDateObj.setDate(checkOutDateObj.getDate() + 1);
        checkOutDateObj.setHours(12, 0, 0);
      }

      checkInStr = checkInDateObj.getFullYear() + "-" + String(checkInDateObj.getMonth()+1).padStart(2,'0') + "-" + String(checkInDateObj.getDate()).padStart(2,'0') + " " + String(checkInDateObj.getHours()).padStart(2,'0') + ":" + String(checkInDateObj.getMinutes()).padStart(2,'0');
      checkOutStr = checkOutDateObj.getFullYear() + "-" + String(checkOutDateObj.getMonth()+1).padStart(2,'0') + "-" + String(checkOutDateObj.getDate()).padStart(2,'0') + " " + String(checkOutDateObj.getHours()).padStart(2,'0') + ":" + String(checkOutDateObj.getMinutes()).padStart(2,'0');

      document.getElementById("displayCheckIn").innerText = checkInStr;
      document.getElementById("displayCheckOut").innerText = checkOutStr;
    }

    async function loadRooms() {
      const container = document.getElementById("roomContainer");
      try {
        const res = await fetch(GAS_API_URL + "?action=getRooms");
        const data = await res.json();
        
        if(data.success) {
          container.innerHTML = "";
          data.rooms.forEach(r => {
            // Kiểm tra trùng lịch
            const isOccupied = data.occupiedSlots.some(s => s.roomId === r.roomId);

            const div = document.createElement("div");
            div.className = `border p-3 rounded-lg cursor-pointer text-center transition ${isOccupied ? 'disabled-slot' : 'hover:border-amber-800'}`;
            div.innerHTML = `<p class="font-bold text-sm">${r.roomName}</p><p class="text-xs text-stone-500">${r.roomType}</p>`;
            
            if(!isOccupied) {
              div.onclick = function() {
                document.querySelectorAll("#roomContainer > div").forEach(d => d.classList.remove("border-amber-800", "bg-amber-100"));
                div.classList.add("border-amber-800", "bg-amber-100");
                selectedRoomId = r.roomId;
              };
            }
            container.appendChild(div);
          });
        }
      } catch(e) {
        container.innerHTML = `<p class="text-xs text-red-500 col-span-2">Không thể tải danh sách phòng!</p>`;
      }
    }

    function fileToBase64(file) {
      return new Promise((resolve, reject) => {
        const reader = new FileReader();
        reader.readAsDataURL(file);
        reader.onload = () => resolve(reader.result);
        reader.onerror = error => reject(error);
      });
    }

    async function handleBookingSubmit(e) {
      e.preventDefault();
      if(!selectedRoomId) { alert("Vui lòng chọn 1 phòng!"); return; }

      const btn = document.getElementById("btnSubmit");
      btn.disabled = true;
      btn.innerText = "Đang xử lý đăng ký...";

      try {
        const idFrontFile = document.getElementById("idFront").files[0];
        const idBackFile = document.getElementById("idBack").files[0];

        const idFrontBase64 = await fileToBase64(idFrontFile);
        const idBackBase64 = await fileToBase64(idBackFile);

        const payload = {
          action: "createBooking",
          data: {
            fullName: document.getElementById("fullName").value,
            phone: document.getElementById("phone").value,
            email: document.getElementById("email").value,
            combo: document.getElementById("comboType").value,
            packageId: document.getElementById("comboType").value,
            roomId: selectedRoomId,
            checkIn: checkInStr,
            checkOut: checkOutStr,
            totalPrice: calculatedTotalPrice,
            idFrontBase64: idFrontBase64,
            idBackBase64: idBackBase64
          }
        };

        const res = await fetch(GAS_API_URL, {
          method: "POST",
          body: JSON.stringify(payload)
        });
        const result = await res.json();

        if(result.success) {
          showPaymentModal(result);
        } else {
          alert("Lỗi: " + result.message);
        }
      } catch(err) {
        alert("Có lỗi xảy ra: " + err.toString());
      } finally {
        btn.disabled = false;
        btn.innerText = "ĐĂNG KÝ & THANH TOÁN";
      }
    }

    function showPaymentModal(res) {
      document.getElementById("qrImage").src = res.vietQrUrl;
      document.getElementById("qrAmount").innerText = Number(res.totalPrice).toLocaleString('vi-VN') + " đ";
      document.getElementById("qrMemo").innerText = res.paymentCode;
      document.getElementById("paymentModal").classList.remove("hidden");

      // Khởi chạy đồng hồ đếm ngược 10 phút
      let timeLeft = 10 * 60;
      clearInterval(timerInterval);
      timerInterval = setInterval(() => {
        const m = Math.floor(timeLeft / 60);
        const s = timeLeft % 60;
        document.getElementById("countdownTimer").innerText = `${String(m).padStart(2,'0')}:${String(s).padStart(2,'0')}`;
        if(--timeLeft < 0) {
          clearInterval(timerInterval);
          alert("Đã hết thời gian 10 phút giữ phòng!");
          closeModal();
        }
      }, 1000);
    }

    function closeModal() {
      document.getElementById("paymentModal").classList.add("hidden");
      location.reload();
    }

    // WEB ADMIN
    async function loadAdminBookings() {
      const pin = document.getElementById("adminPin").value;
      const listDiv = document.getElementById("adminList");
      listDiv.innerHTML = "Đang tải...";

      const res = await fetch(GAS_API_URL, {
        method: "POST",
        body: JSON.stringify({ action: "adminGetBookings", pin: pin })
      });
      const data = await res.json();

      if(!data.success) {
        listDiv.innerHTML = `<p class="text-red-500 text-sm">${data.message}</p>`;
        return;
      }

      listDiv.innerHTML = "";
      data.bookings.forEach(b => {
        const card = document.createElement("div");
        card.className = "bg-white p-4 rounded-xl border space-y-2 text-xs shadow-sm";
        card.innerHTML = `
          <div class="flex justify-between border-b pb-2">
            <span class="font-bold text-amber-900">${b.bookingId} - ${b.fullName}</span>
            <span class="px-2 py-0.5 rounded font-bold ${b.status === 'ĐÃ DUYỆT' ? 'bg-green-100 text-green-700' : 'bg-yellow-100 text-yellow-700'}">${b.status}</span>
          </div>
          <p><b>Phòng:</b> ${b.roomId} | <b>Nội dung CK:</b> <span class="font-bold text-blue-600">${b.paymentCode}</span></p>
          <p><b>SĐT:</b> ${b.phone} | <b>Email:</b> ${b.email}</p>
          <p><b>Thời gian:</b> ${b.checkIn} đến ${b.checkOut}</p>
          <p><b>Số tiền:</b> ${Number(b.totalPrice).toLocaleString('vi-VN')} đ</p>
          <div class="flex gap-2 my-2">
            <a href="${b.idFrontUrl}" target="_blank" class="text-blue-500 underline">CCCD Mặt trước</a>
            <a href="${b.idBackUrl}" target="_blank" class="text-blue-500 underline">CCCD Mặt sau</a>
          </div>
          ${b.status === 'CHỜ THANH TOÁN' ? `<button onclick="approveBooking('${b.bookingId}')" class="w-full bg-green-700 text-white font-bold py-2 rounded-lg mt-2">XÁC NHẬN ĐÃ NHẬN TIỀN & DUYỆT</button>` : ''}
        `;
        listDiv.appendChild(card);
      });
    }

    async function approveBooking(bookingId) {
      const pin = document.getElementById("adminPin").value;
      if(!confirm("Xác nhận đã kiểm tra tài khoản Techcombank và duyệt đơn này?")) return;

      const res = await fetch(GAS_API_URL, {
        method: "POST",
        body: JSON.stringify({ action: "adminApprove", pin: pin, bookingId: bookingId })
      });
      const data = await res.json();
      alert(data.message);
      loadAdminBookings();
    }
  </script>
</body>
</html>
