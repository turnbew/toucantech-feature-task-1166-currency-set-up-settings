FOR PRIVACY AND CODE PROTECTING REASONS THIS IS A SIMPLIFIED VERSION OF CHANGES AND NEW FEATURES

TASK DATE: 23.10.2017 - FINISHED: 08.10.2017

TASK LEVEL: (HARD)  

TASK SHORT DESCRIPTION: 1166 (Currency toggle in settings > set up settings)

GITHUB REPOSITORY CODE: feature/task-1166-currency-set-up-settings

CHANGES

	ADDED new files 
			general_setups_m.php
			general_setups.php
			buttons-multiply-layers.css
	
	IN FILES: 
	
		vent_sign_up2.php
		
			ADDED CHANGED CODE:
			
				//Inside: function populate_summary()
				
				var txt = document.createElement("textarea");
				txt.innerHTML = '<?=default_currency_html_entity?>';
				var currency_entity = txt.value;
				
				
	
		bbevents.php
		
		CHANGED CODE:
		
			FROM:   'currency_code' => 'GBP',
			
			TO:  'currency_code' => default_currency,
	
		form.php
		
			CHANGED CODE:
			
				//At 8 places
				FROM:
				
					&pound;
					
				TO: 
		
					<?php echo $fundraising_default_currency_entity . " " . ...
					
					
		members.php
	
			ADDED CODE: 
			
				//Inside edit function
				$this->load->library('Options');
				.....
				
				$entities = $this->options->currency_html_entities();
				.....
				
				->set('fundraising_default_currency_entity', $entities[Settings::get('fundraising_default_currency')])
	
		front_cart.php
		
			ADDED CODE: 
			
				$input['currency_name'] = default_currency; //Inside checkout() function
	
	
		orders_m.php
	
			CHANGED CODE: 
			
				FROM: 
				
					public function format_order($orders)
					{
						// Get product count
						foreach ($orders AS &$order) {

							// Get product count
							$order['products'] = $this->pyrocache->model('orders_m', 'get_product_count', array($order['id']), $this->firesale->cache_time);

							// No currency set?
							if ($order['currency'] == NULL) {
								$order['currency'] = $this->pyrocache->model('currency_m', 'get', array(), $this->firesale->cache_time);
							}
							
							// Format prices
							$order['price_sub']   = $this->pyrocache->model('currency_m', 'format_string', array($order['price_sub'],   (object) $order['currency'], FALSE), $this->firesale->cache_time);
							$order['price_ship']  = $this->pyrocache->model('currency_m', 'format_string', array($order['price_ship'],  (object) $order['currency'], FALSE), $this->firesale->cache_time);
							$order['price_total'] = $this->pyrocache->model('currency_m', 'format_string', array($order['price_total'], (object) $order['currency'], FALSE), $this->firesale->cache_time);
						}

						return $orders;
					}				
	
	
				TO: 
				
					public function format_order($orders)
					{
						$this->load->library('Options');
						$entities = $this->options->currency_html_entities();						

						// Get product count
						foreach ($orders AS &$order) {

							// Get product count
							$order['products'] = $this->pyrocache->model('orders_m', 'get_product_count', array($order['id']), $this->firesale->cache_time);

							// No currency set?
							if ($order['currency'] == NULL) {
								$order['currency'] = $this->pyrocache->model('currency_m', 'get', array(), $this->firesale->cache_time);
							}
										
							//check that was given unique currency at the order
							if ($order['currency_name'] != '') {
								$order['currency']['cur_format'] =  str_replace($entities, $entities[$order['currency_name']], $order['currency']['cur_format']);
							}	
							
							// Format prices
							$order['price_sub']   = $this->pyrocache->model('currency_m', 'format_string', array($order['price_sub'],   (object) $order['currency'], FALSE), $this->firesale->cache_time);
							$order['price_ship']  = $this->pyrocache->model('currency_m', 'format_string', array($order['price_ship'],  (object) $order['currency'], FALSE), $this->firesale->cache_time);
							$order['price_total'] = $this->pyrocache->model('currency_m', 'format_string', array($order['price_total'], (object) $order['currency'], FALSE), $this->firesale->cache_time);
						}

						return $orders;
					}
	
		fundraising-plugin.css
		
			ADDED CODE: 
			
				@import url("buttons-multiply-layers.css");
	
	
		donate_form_payment.php
		
			CHANGE CODE: 
			
					FROM: <input type="hidden" name="currency_code" value="GBP">
			
					TO: <input type="hidden" name="currency_code" value="<?=default_currency?>">

		
		supportus.css
		fundraising-plugin.css
			
			ADDED CODE: 
					
					@import url("../../../modules/fundraising/css/buttons-multiply-layers.css");

					
		supportus.css
		fundraising.css
			
			ADDED CODE:
			
					@import url("../../fundraising/css/buttons-multiply-layers.css");
					
		
		fundraising.css
		
			
			ADDED CODE: 
			
					@import url("buttons-multiply-layers.css");
		
					
		details.php
		
			Added code 1:
	
				if ($old_version < '2.0.11') 
				{
					$this->_install_general_setups_table($this->_install_general_setups_added_records());

					$table = $this->db->dbprefix('firesale_orders');
					
					if (!$this->db->field_exists('currency_name', $table)) 
					{
						..............
					}
					
					return true;
				}
				
				
			Added code 2:
	
				protected function _install_general_setups_table($records = array()) {
				
					$this->db->query(
						'CREATE TABLE IF NOT EXISTS `' . $this->db->dbprefix('general_setups') . '` (
						  `id` INT UNSIGNED NOT NULL AUTO_INCREMENT PRIMARY KEY,
						  `slug` varchar(100) NOT NULL,
						  `value` text,
						  `updated_on` int(11) NOT NULL default 0,
						  `last_updated_by` int(11)
						) ENGINE=InnoDB  DEFAULT CHARSET=utf8;'
					);	

					if (count($records) > 0) 
					{ 
						..............
					}
				}
				
				
				
				protected function _install_general_setups_added_records() 
				{
					$now = time(); 			
						
					return	array (
								'slug' => 'default_currency',
								'value' => 'GBP', 
								'updated_on' => $now,
								'last_updated_by' => $this->current_user->id
							), 
							array (
								'slug' => 'default_currency_html_entity',
								'value' => '&#163;', 
								'updated_on' => $now,
								'last_updated_by' => $this->current_user->id
							) , 
							array (
								'slug' => 'default_timezone',
								'value' => 'Europe/London;', 
								'updated_on' => $now,
								'last_updated_by' => $this->current_user->id
							) 
						);
				}	
				
	
		menu.php		
			
			added\changed code: 
			
				note: put a new tab into the left-hand side bar
				
					FROM 
						<li <?php if($this->uri->segment(3)=="" or $this->uri->segment(3)=="terms-conditions") echo ' class="active"' ?>><a href="{{ url:site uri='admin-portal/general-settings/terms-conditions' }}">Terms & conditions</a></li>
					
					TO
						<li <?php if($this->uri->segment(3)=="" or $this->uri->segment(3)=="general-setups") echo ' class="active"' ?>><a href="{{ url:site uri='admin-portal/general-settings/general-setups' }}">General setup</a></li>		 
						<li <?php if($this->uri->segment(3)=="terms-conditions") echo ' class="active"' ?>><a href="{{ url:site uri='admin-portal/general-settings/terms-conditions' }}">Terms & conditions</a></li>
		

		
		routes.php
		
		
			added/changed code: 
			
				FROM: $route['network_settings/general_settings/terms-conditions'] = 'general_settings/index';
				
				TO: $route['network_settings/general_settings/general-setups'] = 'general_settings/index';
					$route['network_settings/general_settings/terms-conditions'] = 'general_settings/terms_conditions';
	
	
		index.php

			deleted code: 
			
				<tr>
					<td><a id="edit-currency-settings" class="edit_icon"></a></td>
					<td>Default currency</td>
					<td><input class="form-control" id="disabledInput" type="text" style="width:50px" value="<?=Settings::get('fundraising_default_currency')?>" disabled></td>
				</tr>			
			
			
			
			
		general_settings.php	
	
			deleted code: 
			
				note: deleted everything from index function because was totally same with terms_conditions function
				
						// Begening of deleted part 
							$this->load->model('tc_and_policies_m');
							$this->load->model('bbusers/bbuser_m');
							$this->form_validation->set_rules(
								array(
									'terms_conditions' => array(
										'field' => 'terms_conditions',
										'label' => 'Terms & conditions',
										'rules' => 'trim|required'
									)
								)
							);
							if ($this->form_validation->run()) {
								$result = $this->tc_and_policies_m->update_with_slug('terms_conditions', $this->input->post('terms_conditions'));

								if ($result) {
									$this->session->set_flashdata(array('success' => 'Terms & conditions successfully updated'));
									redirect('admin-portal/general-settings/terms-conditions');
								}
								else {
									$this->session->set_flashdata('error', 'An error occurred while updating the terms & conditions');
								}
							}
							$item = $this->tc_and_policies_m->get_by_slug('terms_conditions');
							if($item->last_updated_by) {
								$item->last_updated_by = $this->bbuser_m->get($item->last_updated_by);
							}
							$this->data['content'] = $item->value;
							$this->data['updated_on'] = $item->updated_on;
							$this->data['last_updated_by'] = $item->last_updated_by;
							$this->data['title'] = 'General Settings';
							$this->template->append_metadata($this->load->view('fragments/wysiwyg', $this->data, TRUE));
							$this->template
								->title('Terms & conditions', ' Admin Portal')
								->build('general_settings/index', $this->data);
						//END of deleted part
					
			added/changed code: 

				note: I created a new file: general_settings, and put a reference into the index() function instead of the deleted code
			
						$this->general_setups();	
				
			added code:
			
				note: I created a new function name is general_setups
				
					public function general_setups()
					{	
						//Load necessary stuffs
						$this->load->model('general_setups_m');
						$this->load->library('Options');
						$currencies = $this->options->currencies();
						$entities = $this->options->currency_html_entities();
						
						//Set form validation rules
						$this->form_validation->set_rules(
							array(
								'default_currency' => array(
									'field' => 'default_currency',
									'label' => 'Default currency',
									'rules' => 'required'
								)
							)
						);		

						................
					}				
								
				
					
		
		Options.php
		
			added code: 
			
				/**
				 * HTML entites of international currencies
				 *
				 * @return array
				 */
				public function currency_html_entites  {
					return array(
						'AED' => '&#1583;',
						'AFN' => '&#65;',
						....... 
					);
				}
	
	
		MY_Controller.php
		
		
			added code: 
			
				//DEFINE general setup settings as constants
				$this->load->model('network_settings/general_setups_m');
				$this->general_setups_m->define_general_setups();
				
	


		sidebar.php
		
		
			added/changed code at 9 places: 
			
				<div class="col-md-3 col-sm-3 donation-button-with-currency">
					<div class="image"><img src="/addons/default/themes/toucantechV2/img/icon-heart-widget-empty.png" alt=""></div>
					<div class="currency"><?=default_currency_html_entity?></div>
				</div>
				
		
		active_campaigns.php
		
		
			added/changed code at 7 places: 
					
						<div class="col-md-3 col-sm-3 donation-button-with-currency" style="min-width: 50px;">
							<div class="image"><img src="/addons/default/themes/toucantechV2/img/icon-heart-widget-empty.png" alt=""></div>
							<div class="currency">	</div>
						</div>	
			
			
			
		news_tagged_campaigns.php
		supportus.php
		content\preview.php
		fundraising\campaigns\snapshot.php
		fundraising\donations\load_more_donation.php
		fundraising\donations\member_profile.php
		fundraising\index.php
		fundraising\partials\child_donations_table.php
		fundraising\partials\sub_campaigns_table.php
		fundraising\rewards\index.php
		fundraising\setup\index.php
		gift_aid_final.php
		active_campaigns.php
		campaign.php
		donate_form.php
		give_online_second.php
		give_online.php
		event_sign_up2.php
		mysupport.php
		cart.php
		
		
			CHANGE: FROM: £ TO: <?=default_currency_html_entity?>
			

		
		event-ticket-type.php
		registration_success.php
		bbevents.php
		bbevent_m.phpű
		details.php
		fundraising.php
		content.php
		
		
			CHANGE: FROM: "£" TO: default_currency_html_entity

		
		give_form.php
		give_blank_form.php
		gift_form.php		
			

			CHANGE: FROM: "£ GBP" TO: default_currency_html_entity ." " . default_currency
					
					
					
		jobs_m.php
		
		
			CHANGE CODE: FROM:  if (preg_match('/[\'^£$%&*()}{@#~?><>,|=_+¬-]/', $keyword)) 
						 TO :  if (preg_match('/[\'^£$€%&*()}{@#~?><>,|=_+¬-]/', $keyword)) 
						 
						 
		invite_import.php
		
		
			CHANGE CODE: FROM:  '£200 Deposit '  => array(
						 TO :   default_currency_html_entity . '200 Deposit '  => array(
						 
						 
		campaigns_m.php
		
			
			CHANGE CODE: FROM:
						$sql  = "SELECT CONCAT(campaign_widget_quick1_text, ' (', '£' , campaign_widget_quick_amount,')') AS customDonationText1";
						$sql .= ", CONCAT(campaign_widget_quick2_text, ' (', '£' , campaign_widget_quick2_amount,')') AS customDonationText2";
						$sql .= ", CONCAT(campaign_widget_quick3_text, ' (', '£' , campaign_widget_quick3_amount,')') AS customDonationText3";
						$sql .= ", CONCAT(campaign_widget_quick4_text, ' (', '£' , campaign_widget_quick4_amount,')') AS customDonationText4";
						$sql .= ", CONCAT(campaign_widget_quick5_text, ' (', '£' , campaign_widget_quick5_amount,')') AS customDonationText5";

		
						TO:
								$sql  = "SELECT CONCAT(campaign_widget_quick1_text, ' (', '" . default_currency_html_entity . "' , campaign_widget_quick_amount,')') AS customDonationText1";
								$sql .= ", CONCAT(campaign_widget_quick2_text, ' (', '" . default_currency_html_entity . "' , campaign_widget_quick2_amount,')') AS customDonationText2";
								$sql .= ", CONCAT(campaign_widget_quick3_text, ' (', '" . default_currency_html_entity . "' , campaign_widget_quick3_amount,')') AS customDonationText3";
								$sql .= ", CONCAT(campaign_widget_quick4_text, ' (', '" . default_currency_html_entity . "' , campaign_widget_quick4_amount,')') AS customDonationText4";
								$sql .= ", CONCAT(campaign_widget_quick5_text, ' (', '" . default_currency_html_entity . "' , campaign_widget_quick5_amount,')') AS customDonationText5";
	
	
		details.php
		
		
			CHANGE CODE: FROM: 
					
							$this->streams->fields->assign_field('fundraising', 'campaigns', 'campaign_quick_any_enabled', array('required' => false, 'instructions' => 'Show quick donate £? button'));
							
						TO: 

							$this->streams->fields->assign_field('fundraising', 'campaigns', 'campaign_quick_any_enabled', array('required' => false, 'instructions' => 'Show quick donate ? button'));
							
