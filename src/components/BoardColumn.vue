<template>
  <AppDragg
    :transferData="{
      type: 'column',
      fromColumnIndex: columnIndex
    }"
  >
    <div
      class="column"
      @drop="moveTaskOrColumn($event, column.tasks, columnIndex)"
      @dragover.prevent
      @dragenter.prevent
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
  </AppDragg>
</template>

<script>
  import ColumnTask from '@/components/ColumnTask'
  import movingTasksAndColumnsMixin from '@/mixins/movingTasksAndColumnsMixin'
  import AppDragg from '@/components/AppDragg'

  export default {
    components: {
      ColumnTask,
      AppDragg
    },
    mixins: [movingTasksAndColumnsMixin],
    methods: {
      createTask(e, tasks){
        this.$store.commit('CREATE_TASK', {
          tasks,
          name: e.target.value
        })
        e.target.value = ''
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
