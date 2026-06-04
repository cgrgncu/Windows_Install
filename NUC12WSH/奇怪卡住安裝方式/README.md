#


### autounattend.xml
+ 要修改下面: CGRG_S007改成其他的，有兩個地方。
```
	<settings pass="oobeSystem">
		<component name="Microsoft-Windows-Shell-Setup" processorArchitecture="amd64" publicKeyToken="31bf3856ad364e35" language="neutral" versionScope="nonSxS">
			<OOBE>
				<ProtectYourPC>3</ProtectYourPC>
				<HideEULAPage>true</HideEULAPage>
				<HideWirelessSetupInOOBE>false</HideWirelessSetupInOOBE>
			</OOBE>
			<UserAccounts>
				<LocalAccounts>
					<LocalAccount wcm:action="add">
						<Password>
							<Value></Value>
							<PlainText>true</PlainText>
						</Password>
						<DisplayName>CGRG_S007</DisplayName>
						<Group>Administrators</Group>
						<Name>CGRG_S007</Name>
					</LocalAccount>
				</LocalAccounts>
			</UserAccounts>
		</component>
	</settings>
```
