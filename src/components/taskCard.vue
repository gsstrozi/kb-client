<template>
	<div
		:class="getCardClass()"
		v-bind:id="task.id"
		v-on:click="openTask">
		<a class="content" v-html="_topContent"></a>		
		<div class="content">
			{{formattedTaskDescription}}
		</div>
		<div v-if="_tags.length > 0" class="content">
			<div
				v-for="tag in _tags"
				:class="getTagClass(tag)">
				{{formattedTag(tag)}}
			</div>
		</div>
		<div class="extra content">
			<img class="ui avatar image" src="../assets/unsigned.png">
		</div>
	</div>
</template>

<script type="text/javascript">
	import wrap from 'greedy-wrap'

	export default {
		props: ['task', 'extraContent', 'topContent'],
		methods: {
			getCardClass () {
				return this.task.status === 'todo' ? 'ui red card'
					: this.task.status === 'progress' ? 'ui yellow card'
					: this.task.status === 'done' ? 'ui green card' : 'ui grey card'
			},
			openTask (e) {
				if (e.target.className === 'ui close icon') {
					this.$emit('confirmDeleteTask', this.task)
				} else {
					this.$emit('openTask', this.task)
				}
			},
			formattedTag (tag) {
				if (typeof tag === 'object') {
					return tag.title
				} else {
					return tag
				}
			},
			getTagClass (tag) {
				if (typeof tag === 'object') {
					return 'ui ' + tag.color + ' label'
				} else {
					return 'ui label'
				}
			}
		},
		computed: {
			formattedSubject () {
				return this.task.subject.length > 50
					? this.task.subject.substr(0, 50)
					: this.task.subject
			},
			formattedTaskDescription () {
				const options = {width: 30, autoWidth: false, linebreak: ' '}
				var description = wrap(this.task.description || '', options)
				return !description ? ''
					: description.length > 100
						? description.substr(0, 100)
						: description
			},
			_tags () {
				return this.task && this.task.tags
					? this.task.tags
					: []
			},
			_topContent () {
				const formattedSubject = this.formattedSubject
				return this.topContent ? this.topContent() : `
					<div class="right floated meta">
						<i class="ui close icon"></i>
					</div>
					${formattedSubject}`
			}
		}
	}
</script>