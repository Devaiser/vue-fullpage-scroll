<template>
  <section class="fullpage one">
    <h1>Section 1</h1>
  </section>
  <section class="fullpage two">
    <h1>Section 2</h1>
  </section>
  <section class="fullpage three">
    <h1>Section 3</h1>
  </section>
  <section class="fullpage four">
    <h1>Section 4</h1>
  </section>
</template>

<script setup>
  import { ref, onMounted, onUnmounted } from "vue";
  const inMove = ref(false);
  const activeSection = ref(0);
  const offsets = ref([]);
  const touchStartY = ref(0);

  const calculateSectionOffsets = () => {
    let sections = document.getElementsByTagName("section");

    for (let i = 0; i < sections.length; i++) {
      const element = sections[i];
      offsets.value.push(element);
    }
  };
  const handleMouseWheel = (e) => {
    if (
      e.wheelDelta < 30 &&
      !inMove.value &&
      activeSection.value !== offsets.value.length - 1
    ) {
      moveUp();
    } else if (e.wheelDelta > 30 && !inMove.value && activeSection.value !== 0) {
      moveDown();
    }
    e.preventDefault();
    return false;
  };
  const handleMouseWheelDOM = (e) => {
    if (
      e.detail > 0 &&
      !inMove.value &&
      activeSection.value !== offsets.value.length - 1
    ) {
      moveUp();
    } else if (e.detail < 0 && !inMove.value && activeSection.value !== 0) {
      moveDown();
    }
  };
  const moveDown = () => {
    if (activeSection.value !== 0) {
      inMove.value = true;
      activeSection.value--;

      if (activeSection.value < 0) {
        activeSection.value = offsets.value.length - 1;
      }
      console.log(activeSection.value);
      scrolltoSection(activeSection.value, true);
    }
  };
  const moveUp = () => {
    if (activeSection.value !== offsets.value.length - 1) {
      inMove.value = true;
      activeSection.value++;

      if (activeSection.value > offsets.value.length - 1) {
        activeSection.value = 0;
      }
      console.log(activeSection.value);

      scrolltoSection(activeSection.value, true);
    }
  };
  const scrolltoSection = (id, force = false) => {
    if (inMove.value && !force) {
      return false;
    }

    activeSection.value = id;
    inMove.value = true;

    document
      .getElementsByTagName("section")
      [id].scrollIntoView({ behavior: "smooth" });

    setTimeout(() => {
      inMove.value = false;
    }, 400);
  };
  const touchStart = (e) => {
    e.preventDefault();

    touchStartY.value = e.touches[0].clientY;
  };
  const touchMove = (e) => {
    if (inMove.value) {
      return false;
    }
    e.preventDefault();

    const currentY = e.touches[0].clientY;

    if (touchStartY.value < currentY) {
      moveDown();
    } else {
      moveUp();
    }

    touchStartY.value = 0;
    return false;
  };
  onMounted(() => {
    calculateSectionOffsets();
    window.addEventListener("DOMMouseScroll", handleMouseWheelDOM);
    window.addEventListener("mousewheel", handleMouseWheel, {
      passive: false,
    });
    window.addEventListener("touchstart", touchStart, {
      passive: false,
    });
    window.addEventListener("touchmove", touchMove, {
      passive: false,
    });
  });
  onUnmounted(() => {
    window.removeEventListener("DOMMouseScroll", handleMouseWheelDOM);
    window.removeEventListener("mousewheel", handleMouseWheel, {
      passive: false,
    });
    window.removeEventListener("touchstart", touchStart, {
      passive: false,
    });
    window.removeEventListener("touchmove", touchMove, {
      passive: false,
    });
  });
</script>

<style>
  body {
    padding: 0;
    margin: 0;
    box-sizing: border-box;
    overflow: hidden;
  }
  h1 {
    margin: 0;
  }
  .fullpage {
    height: 100vh;
    width: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
    color: #fff;
    font-size: 60px;
    font-family: sans-serif;
  }
  .one {
    background: #41b883;
  }
  .two {
    background: #ff5f45;
  }
  .three {
    background: #0798ec;
  }
  .four {
    background: #fc6c7c;
  }
</style>
