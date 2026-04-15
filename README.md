# 🎓 Academic Assessment Portal

Academic Assessment is a high-security web portal built with Vanilla JavaScript, designed to ensure integrity during remote examinations. It uses WebRTC for real-time camera validation and active browser monitoring to prevent cheating.A high-security, web-based examination portal built with Vanilla JavaScript. This portal is designed to prevent academic dishonesty through real-time surveillance and browser-level restrictions.

---

## ✨ Key Features

### 🛡️ Security & Anti-Cheat Measures
* **Biometric Camera Validation:** The exam remains locked until a live camera feed is detected and active.
* **Anti-Screenshot Shield:** Detects `PrintScreen` attempts and common capture shortcuts (Ctrl+S, Ctrl+P, Ctrl+U).
* **Focus Monitoring:** Automatically hides exam content with a "Security Mask" if the user switches tabs or minimizes the browser.
* **Violation Counter:** If a user attempts more than 3 security breaches (screenshots or tab switching), the exam terminates instantly.
* **24-Hour Lockout:** Students who fail (score < 5/10) or are disqualified due to violations are locked out of that subject for 24 hours using `localStorage`.
* **Camera Lock:** Access is denied unless a live laptop camera is detected.
* **Anti-Cheat Triggers:** Detects screenshots, tab switching, and unauthorized shortcuts.Disables right-click (context menu) and blocks common screen-capture keyboard shortcuts.
* **Smart Lockout:** Automatically terminates the exam after 3 violations and enforces a 24-hour retake penalty.
* **Performance Tracking:** Vertical subject navigation with a 5-minute timer and automated result reporting.

### 💻 Technical Highlights
* **Timed Assessments:** 5-minute countdown timer per subject.
* **Responsive Design:** A clean, "Nude/Pastel" aesthetic optimized for laptop displays.
* **Zero Backend Required:** Fully functional as a static site using WebRTC and LocalStorage.

---

## 📚 Included Subjects
1. **DBMS** (Database Management Systems)
2. **Web Technologies**
3. **Mathematics**
4. **COA** (Computer Organization & Architecture)
5. **Computer Networks**

---

## 🛠️ Technical Stack
* **Frontend:** HTML5, CSS3 (Pastel/Nude UI Aesthetic)
* **Logic:** Vanilla JavaScript (ES6+)
* **Hardware Integration:** WebRTC MediaDevices API (Camera Access)
* **Storage:** Browser LocalStorage for persistent lockout tracking
* **Security:** Focus/Blur event listeners and Keydown interception

---

## ⚙️ How to Test
1.  Open the **Live Demo** link.
2.  Allow **Camera Permissions** in your browser.
3.  Select a subject from the vertical homepage list.
4.  Try to switch tabs or take a screenshot to see the security triggers in action!

---

### ⚖️ Disclaimer
This project was developed for educational purposes to demonstrate browser-level security and hardware integration in web applications.

---

## ⚙️ How to Run Locally
1. Clone the repository:
   ```bash
   https://bitbard22.github.io/Academic-assessment-portal/
