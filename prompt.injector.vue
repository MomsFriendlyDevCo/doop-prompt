<script lang="js" frontend>
app.component('promptInjector', {
	data: ()=> ({
		isShowing: false,
		settings: undefined,
	}),
	created() {
		this.$on('$prompt.open', this.openModal);
		this.$on('$prompt.close', this.closeModal);
	},
	methods: {
		openModal(settings) {
			this.settings = settings;
			this.isShowing = true;
			this.$prompt.modal({
				element: `#modal-prompt-${this._uid}`,
				...this.$prompt.settings,
			});
		},
		closeModal() {
			this.isShowing = false;
			$(`#modal-prompt-${this._uid}`).modal('hide');
		},
	},
});
</script>

<template>
	<div class="modal-prompt-injector">
		<div :id="`modal-prompt-${_uid}`" class="modal">
			<dynamic-component
				v-if="isShowing && settings.replace && settings.component"
				:component="settings.component"
				:props="settings.componentProps"
				:events="settings.componentEvents"
			/>
			<div v-else-if="isShowing && settings.replace && settings.isHtml" v-html="settings.body"/>
			<div v-else-if="isShowing" class="modal-dialog" :class="settings.modalClass">
				<div class="modal-content">
					<div class="modal-header">
						<h4 class="modal-title" v-html="settings.title"/>
						<a class="close" @click="$prompt.close(false, 'cancel')"><i class="far fa-times fa-lg"/></a>
					</div>
					<div class="modal-body">
						<div v-if="settings.bodyHeader" v-html="settings.$bodyHeader"/>
						<div v-if="!settings.isHtml" class="text-center">
							<h4>{{settings.body}}</h4>
						</div>
						<div v-if="settings.isHtml" v-html="settings.body"/>
						<dynamic-component
							v-if="settings.component"
							:component="settings.component"
							:props="settings.componentProps"
							:events="settings.componentEvents"
						/>
						<div v-if="settings.bodyFooter" v-html="settings.$bodyFooter"/>
					</div>
					<div v-if="settings.buttons && (settings.buttons.left.length || settings.buttons.right.length || settings.buttons.center.length)" class="modal-footer">
						<div class="align-left">
							<a v-for="button in settings.buttons.left" :key="button.id" @click="button.click()" :class="button.class">
								<i v-if="button.icon" :class="button.icon"/>
								{{button.title}}
							</a>
						</div>
						<div class="align-center">
							<a v-for="button in settings.buttons.center" :key="button.id" @click="button.click()" :class="button.class">
								<i v-if="button.icon" :class="button.icon"/>
								{{button.title}}
							</a>
						</div>
						<div class="align-right">
							<a v-for="button in settings.buttons.right" :key="button.id" @click="button.click()" :class="button.class">
								<i v-if="button.icon" :class="button.icon"/>
								{{button.title}}
							</a>
						</div>
					</div>
				</div>
			</div>
		</div>
	</div>
</template>
