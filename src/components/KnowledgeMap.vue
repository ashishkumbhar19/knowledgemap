<template>
  <div class="p-8">
    <!-- Hexagonal layout with 5 main nodes -->
    <div class="flex justify-center items-center relative">
      <!-- Main nodes -->
      <div
        v-for="(node, index) in nodes"
        :key="node.id"
        :class="[
          'hexagon-container',
          { 'active-node': hoveredNode === index },
          { 'light-node': hoveredNode !== null && hoveredNode !== index }
        ]"
        @mouseover="handleHover(index)"
        @mouseleave="handleMouseLeave"
      >
        <div class="node-content">
          {{ node.label }}
        </div>

        <!-- Sub-nodes always visible -->
        <div
          v-for="(subNode, idx) in subNodes[index + 1]"
          :key="subNode.id"
          class="sub-node hexagon-container"
          :style="subNodeStyle(idx, index)"
          :class="{ 'light-node': hoveredNode !== index, 'active-node': hoveredNode === index }"
        >
          <div class="node-content">{{ subNode.label }}</div>
        </div>
      </div>
    </div>

    <div class="text-center mt-4">
      <span class="font-bold">
        {{ hoveredNode !== null ? `Node ${hoveredNode + 1}` : 'Artificial Intelligence' }}
      </span>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      nodes: [
        { id: 1, label: 'Besic' },
        { id: 2, label: 'Founda tions' },
        { id: 3, label: 'Intermi diate' },
        { id: 4, label: 'Nevig ation' },
        { id: 5, label: 'Pythob robotics' },
      ],
      subNodes: {
        1: [
          { id: 1.1, label: 'Python besic' },
          { id: 1.2, label: 'Rust besic' },
          { id: 1.3, label: 'C++ besic' },
        ],
        2: [
          { id: 2.1, label: 'linux' },
          { id: 2.2, label: 'simulation' },
          { id: 2.3, label: 'ROS' },
        ],
        3: [
          { id: 3.1, label: 'ROS 2' },
          { id: 3.2, label: 'ROS control' },
          { id: 3.3, label: 'Advance c++' },
        ],
        4: [
          { id: 4.1, label: 'RTAB' },
          { id: 4.2, label: 'Nav basic' },
          { id: 4.3, label: 'Nav Adv' },
        ],
        5: [
          { id: 5.1, label: 'robotics' },
          { id: 5.2, label: 'Manupu lation' },
          { id: 5.3, label: 'Robot creaion' },
        ],
      },
      hoveredNode: null, // Tracks the currently hovered node
    };
  },
  methods: {
    handleHover(index) {
      this.hoveredNode = index; // Set hovered node
    },
    handleMouseLeave() {
      this.hoveredNode = null; // Reset when mouse leaves
    },
    subNodeStyle(index, parentIndex) {
      // Define unique positions for sub-nodes based on index and parent node
      const angle = (index * 120) - 60; // Adjust angle to spread sub-nodes
      const radius = 120 + parentIndex * 30; // Adjust distance based on parent
      const offsetX = Math.cos((angle * Math.PI) / 180) * radius; // Calculate x offset
      const offsetY = Math.sin((angle * Math.PI) / 180) * radius; // Calculate y offset

      return {
        transform: `translate(${offsetX}px, ${offsetY}px)`, // Position sub-nodes
        zIndex: parentIndex, // Keep layers organized
      };
    },
  },
};
</script>

<style scoped>
/* Hexagon container */
.hexagon-container {
  width: 50px;
  height: 50px;
  background-color: #f0f0f0;
  display: flex;
  justify-content: center;
  align-items: center;
  border-radius: 50%;
  cursor: pointer;
  position: absolute;
  transition: transform 0.3s ease-in-out, box-shadow 0.3s ease-in-out, background-color 0.3s ease-in-out;
}

/* Active node styles */
.hexagon-container.active-node {
  background-color: #86496d;
  transform: scale(1.2);
  color: white;
  box-shadow: 0 8px 16px rgba(134, 73, 109, 0.5);
}

/* Light node styles for others */
.hexagon-container.light-node {
  background-color: #e7b5d4;
  transform: scale(1);
  color: #000;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
}

/* Content inside the circle */
.node-content {
  font-size: 12px;
  font-weight: bold;
}

/* Positioning for the hexagonal layout */
.hexagon-container:nth-child(1) {
  position: absolute;
  top: 11%;
  left: 50%;
  transform: translateX(-50%);
}

.hexagon-container:nth-child(2) {
  position: absolute;
  top: 40%;
  left: 22%;
  transform: translate(-50%, -50%);
}

.hexagon-container:nth-child(3) {
  position: absolute;
  top: 42%;
  right: 20%;
  transform: translate(50%, -50%);
}

.hexagon-container:nth-child(4) {
  position: absolute;
  bottom: 30%;
  left: 37%;
  transform: translate(-50%, 50%);
}

.hexagon-container:nth-child(5) {
  position: absolute;
  bottom: 25%;
  right: 30%;
  transform: translate(50%, 50%);
}

/* Sub-node styles */
.sub-node {
  width: 50px;
  height: 50px;
  background-color: #e7b5d4;
  border-radius: 50%;
  display: flex;
  justify-content: center;
  align-items: center;
  cursor: pointer;
  text-align: center;
  transition: transform 0.3s ease-in-out, box-shadow 0.3s ease-in-out;
}

.sub-node.active-node {
  background-color: #9c0ad6;
  transform: scale(1.2);
  color: white;
  box-shadow: 0 8px 16px rgba(134, 73, 109, 0.5);
}
</style>
