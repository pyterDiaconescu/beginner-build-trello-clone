<template>
  <div
    class="column"
    draggable
    @drop="moveTaskOrColumn($event, column.tasks, columnIndex)"
    @dragover.prevent
    @dragenter.prevent
    @dragstart.self="pickupColumn($event, columnIndex)"
  >
    <div class="flex items-center mb-2 font-bold">
      {{ column.name }}
    </div>
    <div class="list-reset">
      <ColumnTask
        v-for="(task, taskIndex) of column.tasks"
        :key="taskIndex"
        :task="task"
        :taskIndex="taskIndex"
        :columnIndex="columnIndex"
        :board="board"
        :column="column"
      />
      <input
        type="text"
        class="block p-2 w-full bg-transparent"
        placeholder="+ Enter new task"
        @keyup.enter="createTask($event, column.tasks)"
      >
    </div>
  </div>
</template>

<script>
  import ColumnTask from '@/components/ColumnTask'

  export default {
    name: "BoardColumn",
    components: {
      ColumnTask
    },
    props: {
      column: {
        type: Object,
        required: true
      },
      board: {
        type: Object,
        required: true
      },
      columnIndex: {
        type: Number,
        required: true
      }
    },
    methods: {
      createTask(e, tasks){
        this.$store.commit('CREATE_TASK', {
          tasks,
          name: e.target.value
        })
        e.target.value = ''
      },
      pickupColumn(e, fromColumnIndex){
        e.dataTransfer.effectAllowed = 'move'
        e.dataTransfer.dropEffect = 'move'

        e.dataTransfer.setData('from-column-index', fromColumnIndex)
        e.dataTransfer.setData('type', 'column')
      },
      moveTaskOrColumn(e, toTasks, toColumnIndex, toTaskIndex){
        const type = e.dataTransfer.getData('type')

        if ( type === 'task' ){
          this.moveTask(e, toTasks, toTaskIndex !== 'undefined' ? toTaskIndex : toTasks.length)
        } else {
          this.moveColumn(e, toColumnIndex)
        }
      },
      moveTask(e, toTasks, toTaskIndex){
        const fromColumnIndex = e.dataTransfer.getData('from-column-index')
        const fromTasks = this.board.columns[fromColumnIndex].tasks
        this.$store.commit('MOVE_TASK', {
          fromTasks,
          toTasks,
          fromTaskIndex: e.dataTransfer.getData('from-task-index'),
          toTaskIndex
        })
      },
      moveColumn (e, toColumnIndex) {
        const fromColumnIndex = e.dataTransfer.getData('from-column-index')
        this.$store.commit('MOVE_COLUMN', {
          fromColumnIndex,
          toColumnIndex
        })
      }
    },
  }
</script>

<style scoped>
  .column {
    @apply bg-grey-light p-2 mr-4 text-left shadow rounded;
    min-width: 350px;
  }
</style>
