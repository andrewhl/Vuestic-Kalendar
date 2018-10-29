<template>
    <div>
        <h1>Agenda</h1>
        <kalendar :configuration='calendar_settings' :appointments='appointments'>
          <div slot='popup-form' slot-scope='{popup_scope}' style='display: flex flex-direction: column'>
          <h4 style='margin-bottom: 10px'>New Appointment</h4>
          <input v-model='new_appointment.title' type='text' name='title' placeholder='Title'>
          <textarea v-model='new_appointment.description' type='text' name='description' placeholder='Description' rows='2'></textarea>
          <div class='buttons'>
            <button class='cancel' @click='popup_scope.close_popup = true'>Cancel</button>
            <button @click='completeAppointment(popup_scope, new_appointment)'>Save</button>
          </div>
        </div>
          <div slot='details-card' slot-scope='{appointment_props}'>
                <h4 class='appointment-title'>{{appointment_props.data.title}}</h4>
                <small v-show='(appointment_props.end - appointment_props.start) > 2'>{{appointment_props.data.description}}</small>
                <span class='time'>{{appointment_props.start_value.value | normalizeDate('hh:mm A')}} - {{appointment_props.end_value.value | normalizeDate('hh:mm A')}}</span>
          </div>
        </kalendar>
    </div>
</template>

<script>
  import { Kalendar } from 'kalendar-vue'
  import 'kalendar-vue/dist/KalendarVue.css'
  import { format } from 'date-fns'

  let existingAppointments = [
    {
      from: 'Fri Aug 24 2018 01:00:00',
      to: 'Fri Aug 24 2018 02:00:00',
      date: '2018-08-24',
      data: {
        title: 'Barber Checkin',
        description: 'Lorem ipsum dolor sit amet.'
      }
    },
    {
      from: 'Mon Aug 26 2018 01:00:00',
      to: 'Mon Aug 26 2018 02:00:00',
      date: '2018-08-26',
      data: {
        title: 'My Birthday',
        description: 'Lorem ipsum dolor sit amet.'
      }
    }
  ]


  export default {
    name: 'Agenda',
    components: {
      Kalendar
    },
    data: () => ({
      message: 'hi',
      appointments: existingAppointments,
      calendar_settings: {
        style: 'material_design',
        view_type: 'Month',
        split_value: 20,
        cell_height: 20,
        scrollToNow: false,
        current_day: new Date() // Valid date
      },
      new_appointment: {
        description: null,
        title: null
      }
    }),
    methods: {
      completeAppointment (popData, formData) {
        let payload = {
          data: {
            title: formData.title,
            description: formData.description
          },
          from: popData.appointment_props.start_value.value,
          to: popData.appointment_props.end_value.value,
          date: format(
            popData.appointment_props.start_value.value,
            'YYYY-MM-DD'
          )
        }
        this.appointments.push(payload)
        this.new_appointment = {
          description: null,
          title: null
        }
        popData.close_popup = true
      }
    }
  }
</script>