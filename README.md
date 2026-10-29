# resin
const menuToggle = document.getElementById("menuToggle");
const navLinks = document.getElementById("navLinks");

// Responsive mobile navigation
menuToggle.addEventListener("click", () => {
  const isOpen = navLinks.classList.toggle("open");
  menuToggle.setAttribute("aria-expanded", String(isOpen));
  menuToggle.textContent = isOpen ? "✕" : "☰";
});

navLinks.querySelectorAll("a").forEach(link => {
  link.addEventListener("click", () => {
    navLinks.classList.remove("open");
    menuToggle.setAttribute("aria-expanded", "false");
    menuToggle.textContent = "☰";
  });
});

// Filter artwork by category
const filterButtons = document.querySelectorAll(".filter");
const artCards = document.querySelectorAll(".art-card");

filterButtons.forEach(button => {
  button.addEventListener("click", () => {
    filterButtons.forEach(item => {
      item.classList.remove("active");
      item.setAttribute("aria-pressed", "false");
    });

    button.classList.add("active");
    button.setAttribute("aria-pressed", "true");

    const category = button.dataset.filter;

    artCards.forEach(card => {
      const matches =
        category === "all" ||
        card.dataset.category === category;

      card.classList.toggle("is-hidden", !matches);
    });
  });
});

// Reveal sections as they enter the viewport
const revealElements = document.querySelectorAll(".reveal");

if ("IntersectionObserver" in window) {
  const observer = new IntersectionObserver((entries) => {
    entries.forEach(entry => {
      if (entry.isIntersecting) {
        entry.target.classList.add("visible");
        observer.unobserve(entry.target);
      }
    });
  }, {
    threshold: 0.12
  });

  revealElements.forEach(element => observer.observe(element));
} else {
  revealElements.forEach(element => element.classList.add("visible"));
}

// Automatically update the footer year
document.getElementById("year").textContent =
  new Date().getFullYear();

// Contact form: validate and prepare an email enquiry
const contactForm = document.getElementById("contactForm");
const formStatus = document.getElementById("formStatus");

contactForm.addEventListener("submit", (event) => {
  event.preventDefault();

  if (!contactForm.reportValidity()) return;

  const data = new FormData(contactForm);
  const name = data.get("name").trim();
  const email = data.get("email").trim();
  const interest = data.get("interest");
  const message = data.get("message").trim();

  const subject = encodeURIComponent(
    `Resin art enquiry: ${interest}`
  );

  const body = encodeURIComponent(
    `Hello Kashish,\n\n` +
    `My name is ${name}.\n` +
    `My email: ${email}\n` +
    `I'm interested in: ${interest}\n\n` +
    `My idea:\n${message}\n\n` +
    `Looking forward to hearing from you!`
  );

  formStatus.textContent =
    "Opening your email app to send the enquiry…";

  window.location.href =
    `mailto:?subject=${subject}&body=${body}`;
});
