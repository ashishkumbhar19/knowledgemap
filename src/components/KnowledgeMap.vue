<!-- 
<template>
  <div id="app">
    <svg ref="nodeMap" width="100%" height="100vh"></svg>
  </div>
</template>

<script>
import * as d3 from "d3";

export default {
  name: "NodeMap",
  data() {
    return {
      nodes: [],
      simulation: null,
      linkColors: {
        "Basic ROS2": "purple",
        "Intermediate ROS2": "blue",
        "0. Foundations": "green",
        "Navigation ROS2": "orange",
        "Manipulation": "red",
      },
    };
  },
  mounted() {
    this.createNodeMap();
  },
  methods: {
    createNodeMap() {
      const mainNodes = [
        "Basic ROS2",
        "Intermediate ROS2",
        "0. Foundations",
        "Navigation ROS2",
        "Manipulation",
      ];

      const subNodesMap = {
        "Basic ROS2": [
          "Basic Rust",
          "TF ROS2",
          "URDF Robot Modeling ROS2",
          "Basic C++",
        ],
        "Intermediate ROS2": [
          "ROS2 Control",
          "ROS2 C++",
          "ROS2 Python",
          "Advanced C++",
        ],
        "0. Foundations": [
          "Linux for Robotics",
          "Simulations",
          "ROS Basics",
          "Basics of Robotics",
        ],
        "Navigation ROS2": [
          "RAT B",
          "NAV 2 Basics",
          "New Advances",
          "AWS Basics",
        ],
        Manipulation: [
          "Robotic Theory",
          "Enterprise",
          "Web Development for Robotics",
          "Course Product",
        ],
      };

      const nodes = [
        ...mainNodes.map((id, index) => ({
          id,
          type: "main",
          x: 200 + 400 * (index % 2),
          y: 200 + 400 * Math.floor(index / 2),
        })),
        ...Object.entries(subNodesMap).flatMap(([parent, subNodes]) =>
          subNodes.map(name => ({
            id: name,
            type: "sub",
            parent,
          }))
        ),
      ];

      const links = [
        ...nodes
          .filter(d => d.type === "sub")
          .map(subNode => ({
            source: subNode.parent,
            target: subNode.id,
          })),
        { source: "Basic ROS2", target: "Intermediate ROS2" },
        { source: "Intermediate ROS2", target: "Navigation ROS2" },
        { source: "Navigation ROS2", target: "0. Foundations" },
        { source: "0. Foundations", target: "Basic ROS2" },
        { source: "Manipulation", target: "Basic ROS2" },
        { source: "Manipulation", target: "Intermediate ROS2" },
        { source: "Manipulation", target: "0. Foundations" },
        { source: "Manipulation", target: "Navigation ROS2" },
      ];

      const width = window.innerWidth;
      const height = window.innerHeight;

      const svg = d3.select(this.$refs.nodeMap);

      this.nodes = nodes;

      this.simulation = d3
        .forceSimulation(nodes)
        .force(
          "link",
          d3
            .forceLink(links)
            .id(d => d.id)
            .distance(120)
        )
        .force("charge", d3.forceManyBody().strength(-800))
        .force("center", d3.forceCenter(width / 2, height / 2));

      const link = svg
        .append("g")
        .attr("class", "links")
        .selectAll("line")
        .data(links)
        .join("line")
        .attr("class", "link")
        .style("stroke", d => this.linkColors[d.source.id])
        .style("stroke-width", 1.5);

      const node = svg
        .append("g")
        .attr("class", "nodes")
        .selectAll("circle")
        .data(nodes)
        .join("circle")
        .attr("r", d => (d.type === "main" ? 20 : 10))
        .attr("class", d => (d.type === "main" ? "main-node" : "sub-node"))
        .style("fill", d => (d.type === "main" ? "skyblue" : "orange"))
        .call(
          d3
            .drag()
            .on("start", this.dragStarted)
            .on("drag", this.dragged)
            .on("end", this.dragEnded)
        )
        .on("mouseover", this.handleNodeHoverIn)
        .on("mouseout", this.handleNodeHoverOut)
        .on("click", this.handleNodeClick);

      const labels = svg
        .append("g")
        .attr("class", "labels")
        .selectAll("text")
        .data(nodes)
        .join("text")
        .attr("class", "node-text")
        .attr("text-anchor", "start")
        .attr("dy", 4)
        .attr("x", d => d.x + (d.type === "main" ? 30 : 20))
        .attr("y", d => d.y)
        .text(d => d.id);

      labels.style("fill", "white");

      this.simulation.on("tick", () => {
        link
          .attr("x1", d => d.source.x)
          .attr("y1", d => d.source.y)
          .attr("x2", d => d.target.x)
          .attr("y2", d => d.target.y);

        node.attr("cx", d => d.x).attr("cy", d => d.y);
        labels.attr("x", d => d.x + (d.type === "main" ? 30 : 20)).attr("y", d => d.y);
      });
    },

    dragStarted(event, d) {
      if (!event.active) this.simulation.alphaTarget(0.3).restart();
      d.fx = d.x;
      d.fy = d.y;
    },

    dragged(event, d) {
      d.fx = event.x;
      d.fy = event.y;
    },

    dragEnded(event, d) {
      if (!event.active) this.simulation.alphaTarget(0);
      d.fx = null;
      d.fy = null;
    },

    handleNodeClick(event, d) {
      if (d.type === "main") {
        const connectedNodes = new Set(
          this.nodes.filter(n => n.parent === d.id || n.id === d.id).map(n => n.id)
        );

        d3.selectAll(".node")
          .classed("blurred", n => !connectedNodes.has(n.id));

        d3.selectAll(".link")
          .classed(
            "blurred",
            l =>
              !connectedNodes.has(l.source.id) || !connectedNodes.has(l.target.id)
          );
      }
    },

    handleNodeHoverIn(event, d) {
      if (d.type === "main") {
        const connectedNodes = new Set(
          this.nodes.filter(n => n.parent === d.id || n.id === d.id).map(n => n.id)
        );

        d3.selectAll(".node")
          .style("opacity", n => (connectedNodes.has(n.id) ? 1 : 0.3))
          .style("filter", n => (connectedNodes.has(n.id) ? "none" : "blur(4px)"));

        d3.selectAll(".link")
          .style("opacity", l =>
            (connectedNodes.has(l.source.id) && connectedNodes.has(l.target.id)) ? 1 : 0.3);

        d3.selectAll(".node-text")
          .style("opacity", n => (connectedNodes.has(n.id) ? 1 : 0.3))
          .style("filter", n => (connectedNodes.has(n.id) ? "none" : "blur(4px)"));
      }
    },

    handleNodeHoverOut() {
      d3.selectAll(".node")
        .style("opacity", 1)
        .style("filter", "none");

      d3.selectAll(".link")
        .style("opacity", 1);

      d3.selectAll(".node-text")
        .style("opacity", 1)
        .style("filter", "none");
    },
  },
};
</script>

<style scoped>
.node {
  transition: opacity 0.3s, filter 0.3s;
}

.node-text {
  transition: opacity 0.3s, filter 0.3s;
  fill: white;
}

.link {
  transition: opacity 0.3s;
}
</style>



 -->



 <template>
  <div id="app">
    <svg ref="nodeMap" width="100%" height="100vh"></svg>
  </div>
</template>

<script>
import * as d3 from "d3";

export default {
  name: "NodeMap",
  data() {
    return {
      nodes: [],
      simulation: null,
      linkColors: {
        "Basic ROS2": "purple",
        "Intermediate ROS2": "blue",
        "0. Foundations": "green",
        "Navigation ROS2": "orange",
        "Manipulation": "red",
      },
    };
  },
  mounted() {
    this.createNodeMap();
  },
  methods: {
    createNodeMap() {
      const mainNodes = [
        "Basic ROS2",
        "Intermediate ROS2",
        "0. Foundations",
        "Navigation ROS2",
        "Manipulation",
      ];

      const subNodesMap = {
        "Basic ROS2": [
          "Basic Rust",
          "TF ROS2",
          "URDF Robot Modeling ROS2",
          "Basic C++",
        ],
        "Intermediate ROS2": [
          "ROS2 Control",
          "ROS2 C++",
          "ROS2 Python",
          "Advanced C++",
        ],
        "0. Foundations": [
          "Linux for Robotics",
          "Simulations",
          "ROS Basics",
          "Basics of Robotics",
        ],
        "Navigation ROS2": [
          "RAT B",
          "NAV 2 Basics",
          "New Advances",
          "AWS Basics",
          "Ai besic"
        ],
        Manipulation: [
          "Robotic Theory",
          "Enterprise",
          "Web Development for Robotics",
          "Course Product",
          "Ai interliigence"
        ],
      };

      const nodes = [
        ...mainNodes.map((id, index) => ({
          id,
          type: "main",
          x: 200 + 400 * (index % 2),
          y: 200 + 400 * Math.floor(index / 2),
        })),
        ...Object.entries(subNodesMap).flatMap(([parent, subNodes]) =>
          subNodes.map(name => ({
            id: name,
            type: "sub",
            parent,
          }))
        ),
      ];

      const links = [
        ...nodes
          .filter(d => d.type === "sub")
          .map(subNode => ({
            source: subNode.parent,
            target: subNode.id,
          })),
        { source: "Basic ROS2", target: "Intermediate ROS2" },
        { source: "Intermediate ROS2", target: "Navigation ROS2" },
        { source: "Navigation ROS2", target: "0. Foundations" },
        { source: "0. Foundations", target: "Basic ROS2" },
        { source: "Manipulation", target: "Basic ROS2" },
        { source: "Manipulation", target: "Intermediate ROS2" },
        { source: "Manipulation", target: "0. Foundations" },
        { source: "Manipulation", target: "Navigation ROS2" },
      ];

      const width = window.innerWidth;
      const height = window.innerHeight;

      const svg = d3.select(this.$refs.nodeMap);

      this.nodes = nodes;

      this.simulation = d3
        .forceSimulation(nodes)
        .force(
          "link",
          d3
            .forceLink(links)
            .id(d => d.id)
            .distance(120)
        )
        .force("charge", d3.forceManyBody().strength(-800))
        .force("center", d3.forceCenter(width / 2, height / 2));

      const link = svg
        .append("g")
        .attr("class", "links")
        .selectAll("line")
        .data(links)
        .join("line")
        .attr("class", "link")
        .style("stroke", d => this.linkColors[d.source.id])
        .style("stroke-width", 1.5);

      const node = svg
        .append("g")
        .attr("class", "nodes")
        .selectAll("circle")
        .data(nodes)
        .join("circle")
        .attr("r", d => (d.type === "main" ? 20 : 10))
        .attr("class", d => (d.type === "main" ? "main-node" : "sub-node"))
        .style("fill", d => (d.type === "main" ? "skyblue" : "orange"))
        .call(
          d3
            .drag()
            .on("start", this.dragStarted)
            .on("drag", this.dragged)
            .on("end", this.dragEnded)
        )
        .on("mouseover", this.handleNodeHoverIn)
        .on("mouseout", this.handleNodeHoverOut)
        .on("click", this.handleNodeClick);

      const labels = svg
        .append("g")
        .attr("class", "labels")
        .selectAll("text")
        .data(nodes)
        .join("text")
        .attr("class", "node-text")
        .attr("text-anchor", "start")
        .attr("dy", 4)
        .attr("x", d => d.x + (d.type === "main" ? 30 : 20))
        .attr("y", d => d.y)
        .text(d => d.id);

      labels.style("fill", "white");

      this.simulation.on("tick", () => {
        link
          .attr("x1", d => d.source.x)
          .attr("y1", d => d.source.y)
          .attr("x2", d => d.target.x)
          .attr("y2", d => d.target.y);

        node.attr("cx", d => d.x).attr("cy", d => d.y);
        labels.attr("x", d => d.x + (d.type === "main" ? 30 : 20)).attr("y", d => d.y);
      });
    },

    dragStarted(event, d) {
      if (!event.active) this.simulation.alphaTarget(0.3).restart();
      d.fx = d.x;
      d.fy = d.y;
    },

    dragged(event, d) {
      d.fx = event.x;
      d.fy = event.y;
    },

    dragEnded(event, d) {
      if (!event.active) this.simulation.alphaTarget(0);
      d.fx = null;
      d.fy = null;
    },

    handleNodeClick(event, d) {
      if (d.type === "main") {
        const connectedNodes = new Set(
          this.nodes.filter(n => n.parent === d.id || n.id === d.id).map(n => n.id)
        );

        d3.selectAll(".node")
          .classed("blurred", n => !connectedNodes.has(n.id));

        d3.selectAll(".link")
          .classed(
            "blurred",
            l =>
              !connectedNodes.has(l.source.id) || !connectedNodes.has(l.target.id)
          );
      }
    },

    handleNodeHoverIn(event, d) {
      if (d.type === "main") {
        const connectedNodes = new Set(
          this.nodes.filter(n => n.parent === d.id || n.id === d.id).map(n => n.id)
        );

        d3.selectAll(".node")
          .style("opacity", n => (connectedNodes.has(n.id) ? 1 : 0.3))
          .style("filter", n => (connectedNodes.has(n.id) ? "none" : "blur(4px)"));

        d3.selectAll(".link")
          .style("opacity", l =>
            (connectedNodes.has(l.source.id) && connectedNodes.has(l.target.id)) ? 1 : 0.3);

        d3.selectAll(".node-text")
          .style("opacity", n => (connectedNodes.has(n.id) ? 1 : 0.3))
          .style("filter", n => (connectedNodes.has(n.id) ? "none" : "blur(4px)"));
      }
    },

    handleNodeHoverOut() {
      d3.selectAll(".node")
        .style("opacity", 1)
        .style("filter", "none");

      d3.selectAll(".link")
        .style("opacity", 1);

      d3.selectAll(".node-text")
        .style("opacity", 1)
        .style("filter", "none");
    },
  },
};
</script>

<style scoped>
.node {
  transition: opacity 0.3s, filter 0.3s;
}

.node-text {
  transition: opacity 0.3s, filter 0.3s;
  fill: white;
}

.link {
  transition: opacity 0.3s;
}
</style>



 