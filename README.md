# 📑 Hotjar Custom Events Documentation

## 🎯 Goal
We integrated **Hotjar tracking** into the **flight results page**.  
By default, Hotjar captures **heatmaps** and **session recordings**, but we added **custom events** to track critical user actions in the booking journey.

---

## 🔧 Default Hotjar Setup
Add this snippet in the `<head>` of your JSP page (replace Site ID if needed):

```html
<script>
  (function(h,o,t,j,a,r){
    h.hj=h.hj||function(){(h.hj.q=h.hj.q||[]).push(arguments)};
    h._hjSettings={hjid:Site ID,hjsv:6}; // Hotjar Site ID
    a=o.getElementsByTagName('head')[0];
    r=o.createElement('script'); r.async=1;
    r.src=t+h._hjSettings.hjid+j+h._hjSettings.hjsv;
    a.appendChild(r);
  })(window,document,'https://static.hotjar.com/c/hotjar-','.js?sv=');
</script>
```

👉 This loads Hotjar globally.  
👉 Custom events (explained below) let us track specific behaviors beyond standard heatmaps.

---

## 🧩 Helper Functions
We use a helper to fire events safely after Hotjar is ready:

```html
<script>
function hjReady(cb){ 
  if (window.hj) return cb(); 
  setTimeout(function(){ hjReady(cb); }, 150); 
}

function hjSafeEvent(name){ 
  hjReady(function(){ hj('event', name); }); 
}
</script>
```

---

## 1️⃣ Flight Results Page Viewed
Trigger event when the results page finishes loading.

```html
<script>
document.addEventListener('DOMContentLoaded', function(){
  hjSafeEvent('flight_results_view');
});
</script>
```

**Why:** Confirms that the user reached the results page.

---

## 2️⃣ Flight Cards Rendered
Trigger event when flight cards appear after loader.

```html
<script>
var target = document.querySelector('.loader .l-card');
if (target) {
  new MutationObserver(function(){
    hjSafeEvent('flight_cards_rendered');
  }).observe(target, { childList:true, subtree:true });
}
</script>
```

**Why:** Ensures results were displayed after loader (important for drop-off analysis).

---

## 3️⃣ Book Now Button Clicked
Trigger event when the user clicks any "Book Now" button.

```html
<script>
document.addEventListener('click', function(e){
  var bookNow = e.target.closest('.book-now');
  if (bookNow) hjSafeEvent('book_now_clicked');
});
</script>
```

**Why:** Tracks clicks on the key CTA (user intent to book).

---

## 4️⃣ Fare Rules Viewed
Trigger event when the user opens the Fare Rules modal.

```html
<script>
$('#fare-detail').on('shown.bs.modal', function () {
  hjSafeEvent('fare_rules_viewed');
});
</script>
```

**Why:** Shows engagement with detailed fare conditions.

---

## 5️⃣ Price Change Modal
Trigger event when price increase/decrease modal is shown.

```html
<script>
$('#price-change').on('shown.bs.modal', function () {
  hjSafeEvent('fare_change_modal_opened');
});
</script>
```

**Why:** Detects potential friction in conversion flow.

---

## 6️⃣ Booking Confirmation Modal
Trigger event when the booking confirmation modal is opened.

```html
<script>
$('#bookFlightConfirm').on('shown.bs.modal', function () {
  hjSafeEvent('booking_confirmation_opened');
});
</script>
```

**Why:** Critical point before final purchase.

---

## 7️⃣ Other Important Modals
We also track the opening of other modals:

```html
<script>
$('#email-quote').on('shown.bs.modal', function () {
  hjSafeEvent('email_quote_opened');
});

$('#result-quote').on('shown.bs.modal', function () {
  hjSafeEvent('result_quote_opened');
});

$('#errorFareCheck').on('shown.bs.modal', function () {
  hjSafeEvent('error_fare_check');
});
</script>
```
## ⚠️ Why Hotjar Won’t Work on Localhost or IP
Hotjar does **not capture sessions on `localhost` or raw IPs** (e.g., `192.168.x.x`).  
This happens because Hotjar only tracks traffic on **whitelisted domain names** you set in its dashboard.  

- `http://localhost:8080/...` ❌ Not supported  
- `http://192.168.1.70/...` ❌ Not supported  


  192.168.1.70   myproject.local
  ```  
  Then open the site as `http://myproject.local:8080/...` and add `myproject.local` in Hotjar’s allowed domains.  

**Why:** Helps analyze engagement and errors during the booking process.

---

