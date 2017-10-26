TASK DATE: 23.11.2017

TASK SHORT DESCRIPTION: 1166 (Currency toggle in settings > set up settings)

GITHUB REPOSITORY CODE: feature/task-1166-currency-set-up-settings

ORIGINAL WORK: https://github.com/BusinessBecause/network-site/tree/feature/task-1166-currency-set-up-settings

CHANGES

	IN FILES: 
	
		Added new file: C:\toucantech.dev\network-site\addons\default\modules\network_settings\models\general_setups_m.php
		
		
		Added new file: C:\toucantech.dev\network-site\addons\default\modules\network_settings\views\general_settings\general_setups.php
	
	
		Added new file: C:\toucantech.dev\network-site\addons\default\themes\toucantechV2\css\buttons-multiply-layers.css
	
	
		C:\toucantech.dev\network-site\addons\default\themes\toucantechV2\css\fundraising-plugin.css
		
			ADDED CODE: 
			
				@import url("buttons-multiply-layers.css");
	
	
		C:\toucantech.dev\network-site\addons\default\modules\fundraising\views\partials\donate_form_payment.php
		
			CHANGE CODE: 
			
					FROM: <input type="hidden" name="currency_code" value="GBP">
			
					TO: <input type="hidden" name="currency_code" value="<?=default_currency?>">

		
		C:\toucantech.dev\network-site\addons\default\themes\toucantechV2\css\supportus.css
		C:\toucantech.dev\network-site\addons\default\themes\toucantechV2\css\fundraising-plugin.css
			
			ADDED CODE: 
					
					@import url("../../../modules/fundraising/css/buttons-multiply-layers.css");

					
		C:\toucantech.dev\network-site\addons\default\modules\fundraising\css\supportus.css
		C:\toucantech.dev\network-site\addons\default\modules\network_settings\css\fundraising.css
			
			ADDED CODE:
			
					@import url("../../fundraising/css/buttons-multiply-layers.css");
					
		
		C:\toucantech.dev\network-site\addons\default\modules\fundraising\css\fundraising.css
		
			
			ADDED CODE: 
			
					@import url("buttons-multiply-layers.css");
		
					
		C:\toucantech.dev\network-site\addons\default\modules\firesale\details.php
		
			Added code 1:
	
				 // Add new table with two basic records
				if ($old_version < '1.2.9') {
					$this->_install_general_setups_table($this->_install_general_setups_basic_records());	
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
						foreach ($records as $key => $record) 
							$this->db->insert($this->db->dbprefix('general_setups'), $record);
				}
				
				
				
				protected function _install_general_setups_basic_records() 
				{
					$now = time(); 			
						
					return	array (	
								array (
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
								) 
							);
				}	
				
	
		C:\toucantech.dev\network-site\addons\default\modules\network_settings\views\general_settings\partials\menu.php		
			
			added\changed code: 
			
				note: put a new tab into the left-hand side bar
				
					FROM 
						<li <?php if($this->uri->segment(3)=="" or $this->uri->segment(3)=="terms-conditions") echo ' class="active"' ?>><a href="{{ url:site uri='admin-portal/general-settings/terms-conditions' }}">Terms & conditions</a></li>
					
					TO
						<li <?php if($this->uri->segment(3)=="" or $this->uri->segment(3)=="general-setups") echo ' class="active"' ?>><a href="{{ url:site uri='admin-portal/general-settings/general-setups' }}">General setup</a></li>		 
						<li <?php if($this->uri->segment(3)=="terms-conditions") echo ' class="active"' ?>><a href="{{ url:site uri='admin-portal/general-settings/terms-conditions' }}">Terms & conditions</a></li>
		

		
		C:\toucantech.dev\network-site\addons\default\modules\network_settings\config\routes.php
		
		
			added/changed code: 
			
				FROM: $route['network_settings/general_settings/terms-conditions'] = 'general_settings/index';
				
				TO: $route['network_settings/general_settings/general-setups'] = 'general_settings/index';
					$route['network_settings/general_settings/terms-conditions'] = 'general_settings/terms_conditions';
	
	
		C:\toucantech.dev\network-site\addons\default\modules\network_settings\views\fundraising\setup\index.php
		
		
		deleted code: 
		
			<tr>
				<td><a id="edit-currency-settings" class="edit_icon"></a></td>
				<td>Default currency</td>
				<td><input class="form-control" id="disabledInput" type="text" style="width:50px" value="<?=Settings::get('fundraising_default_currency')?>" disabled></td>
			</tr>			
			
			
			
			
		C:\toucantech.dev\network-site\addons\default\modules\network_settings\controllers\general_settings.php	
	
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

						//Update/save value into database and checking process
						$result = true;
						if ($this->form_validation->run()) {
							$default_currency = $this->input->post('default_currency');
							$default_currency_html_entity = ($entities[$default_currency] == '') 			
																		? $default_currency 
																		: $entities[$default_currency];			
							$result = $this->general_setups_m->update_with_slug('default_currency', $this->input->post('default_currency'));
							if ($result) $result = $this->general_setups_m->update_with_slug('default_currency_html_entity', $default_currency_html_entity);
							if ($result) {
								$this->session->set_flashdata(array('success' => 'General setups are updated!'));
								redirect('admin-portal/general-settings/general-setups');
							}
							else {
								$this->session->set_flashdata('error', 'An error occurred while updating');
							}
						}
						
						//Get currencies from \network-site\addons\shared_addons\libraries\Options.php file 
						foreach ($currencies as $int_sign => &$currency_name) $currency_name = $int_sign . " - " . $currency_name;
						$default_currency_selected = $this->general_setups_m->get_by_slug('default_currency')->value;			

						
						//Get template
						$this->data['title'] = 'General setup';		
						$this->template
							->set('currencies', $currencies)
							->set('default_currency_selected', $default_currency_selected)
							->title('General setup', ' Admin Portal')
							->build('general_settings/general_setups', $this->data);
					}				
								
				
					
		
		C:\toucantech.dev\network-site\addons\shared_addons\libraries\Options.php
		
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
	
	
		C:\toucantech.dev\network-site\system\cms\core\MY_Controller.php
		
		
			added code: 
			
				//DEFINE general setup settings as constants
				$this->load->model('network_settings/general_setups_m');
				$this->general_setups_m->define_general_setups();
				
	


		C:\toucantech.dev\network-site\addons\default\modules\fundraising\views\plugin\sidebar.php
		
		
			added/changed code at 9 places: 
			
				<div class="col-md-3 col-sm-3 donation-button-with-currency">
					<div class="image"><img src="/addons/default/themes/toucantechV2/img/icon-heart-widget-empty.png" alt=""></div>
					<div class="currency"><?=default_currency_html_entity?></div>
				</div>
				
		
		C:\toucantech.dev\network-site\addons\default\modules\fundraising\views\partials\active_campaigns.php
		
		
			added/changed code at 7 places: 
					
						<div class="col-md-3 col-sm-3 donation-button-with-currency" style="min-width: 50px;">
							<div class="image"><img src="/addons/default/themes/toucantechV2/img/icon-heart-widget-empty.png" alt=""></div>
							<div class="currency"><?=default_currency_html_entity?></div>
						</div>	
			
		C:\toucantech.dev\network-site\addons\default\modules\fundraising\views\partials\news_tagged_campaigns.php
		C:\toucantech.dev\network-site\addons\default\modules\fundraising\views\supportus.php
		C:\toucantech.dev\network-site\addons\default\modules\network_settings\views\content\preview.php
		C:\toucantech.dev\network-site\addons\default\modules\network_settings\views\fundraising\campaigns\snapshot.php
		C:\toucantech.dev\network-site\addons\default\modules\network_settings\views\fundraising\donations\load_more_donation.php
		C:\toucantech.dev\network-site\addons\default\modules\network_settings\views\fundraising\donations\member_profile.php
		C:\toucantech.dev\network-site\addons\default\modules\network_settings\views\fundraising\index.php
		C:\toucantech.dev\network-site\addons\default\modules\network_settings\views\fundraising\partials\child_donations_table.php
		C:\toucantech.dev\network-site\addons\default\modules\network_settings\views\fundraising\partials\sub_campaigns_table.php
		C:\toucantech.dev\network-site\addons\default\modules\network_settings\views\fundraising\rewards\index.php
		C:\toucantech.dev\network-site\addons\default\modules\network_settings\views\fundraising\setup\index.php
		C:\toucantech.dev\network-site\addons\default\modules\fundraising\views\partials\gift_aid_final.php
		C:\toucantech.dev\network-site\addons\default\modules\fundraising\views\partials\active_campaigns.php
		C:\toucantech.dev\network-site\addons\default\modules\fundraising\views\partials\campaign.php
		C:\toucantech.dev\network-site\addons\default\modules\fundraising\views\partials\donate_form.php
		C:\toucantech.dev\network-site\addons\default\modules\fundraising\views\partials\give_online_second.php
		C:\toucantech.dev\network-site\addons\default\modules\fundraising\views\partials\give_online.php
		C:\toucantech.dev\network-site\addons\default\modules\bbevents\views\event_sign_up2.php
		C:\toucantech.dev\network-site\addons\default\modules\fundraising\views\mysupport.php
		C:\toucantech.dev\network-site\addons\default\modules\firesale\views\cart.php
		
		
			CHANGE: FROM: £ TO: <?=default_currency_html_entity?>
			

		
		C:\toucantech.dev\network-site\addons\default\modules\network_settings\views\content\tables\event-ticket-type.php
		C:\toucantech.dev\network-site\addons\default\modules\bbevents\views\registration_success.php
		C:\toucantech.dev\network-site\addons\default\modules\bbevents\controllers\bbevents.php
		C:\toucantech.dev\network-site\addons\default\modules\bbevents\models\bbevent_m.phpű
		C:\toucantech.dev\network-site\addons\default\modules\firesale\details.php
		C:\toucantech.dev\network-site\addons\default\modules\fundraising\controllers\fundraising.php
		C:\toucantech.dev\network-site\addons\default\modules\network_settings\controllers\content.php
		
		
			CHANGE: FROM: "£" TO: default_currency_html_entity

		
		C:\toucantech.dev\network-site\addons\default\modules\fundraising\views\partials\give_form.php
		C:\toucantech.dev\network-site\addons\default\modules\fundraising\views\partials\give_blank_form.php
		C:\toucantech.dev\network-site\addons\default\modules\fundraising\views\partials\gift_form.php		
			

			CHANGE: FROM: "£ GBP" TO: default_currency_html_entity ." " . default_currency
					
					
					
		C:\toucantech.dev\network-site\addons\default\modules\jobs\models\jobs_m.php
		
		
			CHANGE CODE: FROM:  if (preg_match('/[\'^£$%&*()}{@#~?><>,|=_+¬-]/', $keyword)) 
						 TO :  if (preg_match('/[\'^£$€%&*()}{@#~?><>,|=_+¬-]/', $keyword)) 
						 
						 
		C:\toucantech.dev\network-site\addons\default\modules\network_settings\controllers\invite_import.php
		
		
			CHANGE CODE: FROM:  '£200 Deposit '  => array(
						 TO :   default_currency_html_entity . '200 Deposit '  => array(
						 
						 
		C:\toucantech.dev\network-site\addons\default\modules\fundraising\models\campaigns_m.php
		
			
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
	
	
		C:\toucantech.dev\network-site\addons\default\modules\fundraising\details.php
		
		
			CHANGE CODE: FROM: 
					
							$this->streams->fields->assign_field('fundraising', 'campaigns', 'campaign_quick_any_enabled', array('required' => false, 'instructions' => 'Show quick donate £? button'));
							
						TO: 

							$this->streams->fields->assign_field('fundraising', 'campaigns', 'campaign_quick_any_enabled', array('required' => false, 'instructions' => 'Show quick donate ? button'));
							
